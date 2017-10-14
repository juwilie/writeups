L33t Hard (rev 300)
========
Link: https://hackyou.ctf.su/files/leethard.zip

Video: https://cloud.mail.ru/public/L46M/cNFjfnSyh

Checker: https://hy17-leethard.spb.ctf.su/

exploit:

```c++
#include <iostream>

uint32_t stage7(uint32_t accumulator, std::string stages) {
    uint32_t f = accumulator + 701881;

    if (stages[6] == '1')
        accumulator = f;
    if (stages[6] == '2')
        accumulator = f << 1;
    if (stages[6] == '3')
        accumulator = (f << 1) + f;
    if (stages[6] == '4')
        accumulator = f << 2;

    return accumulator;
}

uint32_t stage6(uint32_t accumulator, std::string stages) {
    uint32_t e = accumulator + 118067;

    if (stages[5] == '1')
        accumulator = e;
    if (stages[5] == '2')
        accumulator = e << 1;
    if (stages[5] == '3')
        accumulator = (e << 1) + e;
    if (stages[5] == '4')
        accumulator = e << 2;

    return stage7(accumulator, stages);
}

uint32_t stage5(uint32_t accumulator, std::string stages) {
    uint32_t d = accumulator + 101591;

    if (stages[4] == '1')
        accumulator = d;
    if (stages[4] == '2')
        accumulator = d << 1;
    if (stages[4] == '3')
        accumulator = (d << 1) + d;
    if (stages[4] == '4')
        accumulator = d << 2;

    return stage6(accumulator, stages);
}

uint32_t stage4(uint32_t accumulator, std::string stages) {
    uint32_t c = accumulator + 151583;

    if (stages[3] == '1')
        accumulator = c;
    if (stages[3] == '2')
        accumulator = c << 1;
    if (stages[3] == '3')
        accumulator = (c << 1) + c;
    if (stages[3] == '4')
        accumulator = c << 2;

    return stage5(accumulator, stages);
}

uint32_t stage3(uint32_t accumulator, std::string stages) {
    uint32_t b = accumulator + 385656;

    if (stages[2] == '1')
        accumulator = b;
    if (stages[2] == '2')
        accumulator = b << 1;
    if (stages[2] == '3')
        accumulator = (b << 1) + b;
    if (stages[2] == '4')
        accumulator = b << 2;

    return stage4(accumulator, stages);
}

uint32_t stage2(uint32_t accumulator, std::string stages) {
    uint32_t a = accumulator + 787242;

    if (stages[1] == '1')
        accumulator = a;
    if (stages[1] == '2')
        accumulator = a << 1;
    if (stages[1] == '3')
        accumulator = (a << 1) + a;
    if (stages[1] == '4')
        accumulator = a << 2;

    return stage3(accumulator, stages);
}

uint32_t stage1(std::string stages) {
    uint32_t defconst = 646947;
    uint32_t accumulator;
    if (stages[0] == '1')
        accumulator = defconst;
    if (stages[0] == '2')
        accumulator = defconst * 2;
    if (stages[0] == '3')
        accumulator = defconst * 3;
    if (stages[0] == '4')
        accumulator = defconst * 4;

    return stage2(accumulator, stages);
}

int main() {
    uint32_t result = 2668432671; // 1337 binary
    std::string stages = "4344443"; // le37 by default for check
    std::cout << stage1(stages) << " - le37 value " << std::endl;
    for (stages[0] = '1'; stages[0] <= '4'; stages[0]++) {
        for (stages[1] = '1'; stages[1] <= '4'; stages[1]++) {
            for (stages[2] = '1'; stages[2] <= '4'; stages[2]++) {
                for (stages[3] = '1'; stages[3] <= '4'; stages[3]++) {
                    for (stages[4] = '1'; stages[4] <= '4'; stages[4]++) {
                        for (stages[5] = '1'; stages[5] <= '4'; stages[5]++) {
                            for (stages[6] = '1'; stages[6] <= '4'; stages[6]++) {
                                if (stage1(stages) == result)
                                    std::cout << stages << " - 1337 keys " << std::endl;
                            }
                        }
                    }
                }
            }
        }
    }
    return 0;
}
```

