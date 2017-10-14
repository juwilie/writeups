Explanation coming soon

```python
import sys
import urllib2
import struct

def getfileheaders(from1):
    req = urllib2.Request(url)
    req.headers['Range'] = 'bytes='+str(int(from1)+6)+'-'+str(int(from1)+7)
    f = urllib2.urlopen(req)
    val = f.read()
    header_data = struct.unpack('<h', val)[0]
    req.headers['Range'] = 'bytes='+str(int(from1)+8)+'-'+str(int(from1)+11)
    f = urllib2.urlopen(req)
    val = f.read()
    file_data = struct.unpack('<i', val)[0]
    print(file_data, header_data)
    req = urllib2.Request(url)
    req.headers['Range'] = 'bytes='+from1+'-'+str(int(from1)+header_data)
    print str(int(from1)+header_data)
    f = urllib2.urlopen(req)
    file.write(f.read())
    file.seek(int(from1)+header_data+file_data-1)
    print int(from1)+header_data+file_data
    file.write('\0')
    return str(int(from1)+header_data+file_data)

def getfilecontent(from1):
    req = urllib2.Request(url)
    req.headers['Range'] = 'bytes='+str(int(from1)+6)+'-'+str(int(from1)+7)
    f = urllib2.urlopen(req)
    val = f.read()
    header_data = struct.unpack('<h', val)[0]
    req.headers['Range'] = 'bytes='+str(int(from1)+8)+'-'+str(int(from1)+11)
    f = urllib2.urlopen(req)
    val = f.read()
    file_data = struct.unpack('<i', val)[0]
    print(file_data, header_data)
    req = urllib2.Request(url)
    req.headers['Range'] = 'bytes='+from1+'-'+str(int(from1)+header_data)
    print str(int(from1)+header_data)
    f = urllib2.urlopen(req)
    file.write(f.read())

    req = urllib2.Request(url)
    req.headers['Range'] = 'bytes='+str(int(from1)+header_data+1)+'-'+str(int(from1)+header_data+file_data)
    print str(int(from1)+header_data)
    f = urllib2.urlopen(req)
    file.write(f.read())

    print int(from1)+header_data+file_data
    return str(int(from1)+header_data+file_data)

fileName = 'network.rar.1'
url = 'https://hy17-hugerar.spb.ctf.su/network200.rar'
req = urllib2.Request(url)
req.headers['Range'] = 'bytes=0-169'
f = urllib2.urlopen(req)
file = open(fileName, 'w')
file.write(f.read())
file.seek(1458688168)
file.write('\0')

next = getfileheaders('1458688169')
next = getfileheaders(next)
next = getfileheaders(next)
next = getfileheaders(next)
next = getfileheaders(next)
next = getfileheaders(next)
next = getfileheaders(next)
next = getfileheaders(next)
next = getfileheaders(next)
next = getfileheaders(next)
next = getfileheaders(next)
next = getfileheaders(next)
next = getfileheaders(next)
next = getfileheaders(next)
next = getfileheaders(next)
next = getfileheaders(next)
next = getfilecontent(next)

```
