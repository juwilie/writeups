Нужно: кастомный ответ на ping с измененным timestamp.

```c
#include <stdlib.h>
#include <unistd.h>
#include <fcntl.h>
#include <errno.h>
#include <sys/wait.h>
#include <sys/socket.h>
#include <resolv.h>
#include <netdb.h>
#include <netinet/in.h>
#include <netinet/ip_icmp.h>
#include <arpa/inet.h>
#include <string.h>
#include <strings.h>
#include <pthread.h>

#define PACKETSIZE	64
struct packet
{
	struct icmphdr hdr;
	char msg[PACKETSIZE-sizeof(struct icmphdr)];
};


struct protoent *proto=NULL;
unsigned int usecs_before;
unsigned int usecs_after;

unsigned short checksum(void *b, int len)
{	unsigned short *buf = b;
	unsigned int sum=0;
	unsigned short result;

	for ( sum = 0; len > 1; len -= 2 )
		sum += *buf++;
	if ( len == 1 )
		sum += *(unsigned char*)buf;
	sum = (sum >> 16) + (sum & 0xFFFF);
	sum += (sum >> 16);
	result = ~sum;
	return result;
}

void display(void *buf, int bytes)
{	int i;
	struct in_addr saddr;
	struct in_addr daddr;
	struct iphdr *ip = buf;
	struct icmphdr *icmp = buf+ip->ihl*4;


	bzero(&saddr, sizeof(struct in_addr));
	bzero(&daddr, sizeof(struct in_addr));
	saddr.s_addr = ip->saddr;
	daddr.s_addr = ip->daddr;

	printf("----------------\n");
	for ( i = 0; i < bytes; i++ )
	{
		if ( !(i & 15) ) printf("\n%02X:  ", i);
		printf("%02X ", ((unsigned char*)buf)[i]);
	}
	printf("\n");
	printf("IPv%d: hdr-size=%d pkt-size=%d protocol=%d TTL=%d src=%s ",
		ip->version, ip->ihl*4, ntohs(ip->tot_len), ip->protocol,
		ip->ttl, inet_ntoa(saddr));
	printf("dst=%s\n", inet_ntoa(daddr));
	printf("ICMP: type[%d/%d] checksum[%d] id[%d] seq[%d]\n",
		icmp->type, icmp->code, ntohs(icmp->checksum),
		icmp->un.echo.id, icmp->un.echo.sequence);
}

void ping_reply(void *buf, int bytes)
{
	int i, sd, ttl, pckt_size;
	struct packet pckt;
	struct sockaddr_in r_addr;
	struct sockaddr_in s_addr;
	struct in_addr saddr;

	uint32_t *timestamp;

	pckt_size = bytes - sizeof(struct iphdr);
	struct iphdr *ip = buf;
	memcpy(&pckt, buf+ip->ihl*4, pckt_size);
	bzero(&saddr, sizeof(struct in_addr));
	saddr.s_addr = ip->saddr;
	ttl = ip->ttl / 2;

	r_addr.sin_family = AF_INET;
	r_addr.sin_port = 0;
	r_addr.sin_addr = saddr;

	sd = socket(PF_INET, SOCK_RAW, proto->p_proto);
	if ( sd < 0 )
	{
		perror("socket");
		return;
	}
	if ( setsockopt(sd, SOL_IP, IP_TTL, &ttl, sizeof(ttl)) != 0)
		perror("Set TTL option");
	if ( fcntl(sd, F_SETFL, O_NONBLOCK) != 0 )
		perror("Request nonblocking I/O");

	pckt.hdr.type = ICMP_ECHOREPLY;

	{

		socklen_t len=sizeof(s_addr);
		if ( recvfrom(sd, &pckt, sizeof(pckt), 0, (struct sockaddr*)&s_addr, &len) > 0 )
			printf("recieved\n");
		
		pckt.hdr.checksum = 0;
		uint32_t *timestamp = (uint32_t *)pckt.msg;
		printf("%i\n",*timestamp);
		(*timestamp)++;
		printf("%i\n",*timestamp);
		pckt.hdr.checksum = checksum(&pckt, pckt_size);
		if ( sendto(sd, &pckt, pckt_size, 0, (struct sockaddr*)&r_addr, sizeof(r_addr)) <= 0 )
			perror("send");
	}

}

void listener(void)
{	int sd;
	struct sockaddr_in addr;
	unsigned char buf[1024];

	sd = socket(PF_INET, SOCK_RAW, proto->p_proto);
	if ( sd < 0 )
	{
		perror("socket");
		exit(0);
	}
	for (;;)
	{	int bytes; socklen_t len=sizeof(addr);

		bzero(buf, sizeof(buf));
		bytes = recvfrom(sd, buf, sizeof(buf), 0, (struct sockaddr*)&addr, &len);
		if ( bytes > 0 )
		{
			display(buf, bytes);
			ping_reply(buf, bytes);
		}
		else
			perror("recv");
	}
	exit(0);
}

int main(int argc, char const *argv[])
{
	proto = getprotobyname("ICMP");
	listener();
	return 0;
}
```
