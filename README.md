# b01lersCTF-rust1
writeup for rust1

Open up `rust::main` for analysis

![image](https://user-images.githubusercontent.com/7328587/165008653-b3c2da5f-d431-4883-95c7-d052367b48fc.png)

The first few lines are data initialization, so we don't really bother about them

You don't kill the beast
---

The start of the loop for the main program to run can be found further down.

The first case is when you slay the beast (beast health == 0)

![image](https://user-images.githubusercontent.com/7328587/165008754-d0e060ce-e11f-4790-b9ec-597a07a4e74b.png)

If in the event you kill it, it prints `"BUT WHERE'S THE FLAG???"`, and goes to label `LAB_0010aeaa;`, which is important

![image](https://user-images.githubusercontent.com/7328587/165008892-0ef315cf-35d9-493a-b82f-0b924ed6d5f5.png)

We skip down to inspect what `LAB_0010aeaa;` does, and it sets `bVar4 = false;`

However, further down in the same block of code, we see that we need `bVar4` to be true in order to print the flag. So killing the beast isn't the point of this challenge.

![image](https://user-images.githubusercontent.com/7328587/165009055-4363296f-65f8-4072-8151-2ffa89a3d958.png)

Instead, we try to find where it sets `bVar4` to be `true`, which is found at the end of one of the code blocks

![image](https://user-images.githubusercontent.com/7328587/165009242-98b7d8ec-015e-4cc9-a479-4f6a26e43bd7.png)

Scrolling all the way to to the head of the code block, we see that it's the block that triggers when you enter a power up

![image](https://user-images.githubusercontent.com/7328587/165009355-277e16b2-cb4e-4278-850b-e45f7bb12c2b.png)

Constrains
---

The code that follows at the bottom are the checks that your input needs to pass in order for the control flow to reach all the way to the bottom, where `bVar4` is set to true. I've written them from index 0, so char 1 is really the 2nd index of the input.

Has to be 22 chars long

![image](https://user-images.githubusercontent.com/7328587/165009595-2e75e12d-69ed-4ec1-a298-9085892185d5.png)

First 4 chars need to add up to `0x115`

![image](https://user-images.githubusercontent.com/7328587/165009629-9f44b35a-7e2b-48a0-8282-f3c5072755dd.png)

chars 4- 7 need to add up to `0x1337`

![image](https://user-images.githubusercontent.com/7328587/165009694-1165a504-36d8-47ae-9b1c-512c676e2253.png)

shifitng the equation, the chars need to be below `0x7e`, or `127`, which is the max range of ASCII characters

![image](https://user-images.githubusercontent.com/7328587/165009732-dd2a9ea3-8650-4104-bda5-83364fa7ce08.png)

chars 9 + char 10  >= 100

![image](https://user-images.githubusercontent.com/7328587/165009846-e4bf9701-0f12-4c86-b16c-0a5e8a0e05cc.png)

char 11 needs to be 2x char 10

![image](https://user-images.githubusercontent.com/7328587/165009891-33863b22-0c0b-4e9b-84a8-4570731e9298.png)

char 12 * char 13  >= `0x3e9`

![image](https://user-images.githubusercontent.com/7328587/165009965-72187e8c-ec8d-463b-bd74-5c428eeead4e.png)

(char 14 + char 15) * 5  >= `0xc9`

![image](https://user-images.githubusercontent.com/7328587/165010020-5e315fb2-5467-4e0e-9e0d-d6dce00ac6cd.png)

char 16 - char 17 == '*'

![image](https://user-images.githubusercontent.com/7328587/165010092-2ac0bec9-e2e5-4b4e-a8d3-a6d9180597dc.png)

char 18 - char 19 == '\x02'

![image](https://user-images.githubusercontent.com/7328587/165010250-52b7c894-9864-4947-91f5-50481f933725.png)

char 20 * char 21 >= 900

![image](https://user-images.githubusercontent.com/7328587/165010322-3f54bd1a-8d71-4271-acd8-7ed0da335636.png)

Z3
---

Using those constrains, we pluck out a Z3 equation to get the correct input

```
from z3 import *
z = Solver()

power = [BitVec('power{:02}'.format(i), 32) for i in range(22)]

# setting constrains for every key value
for i in power:
    z.add(i >=33, i <= 126)

z.add(power[0] + power[1] + power[2] + power[3] == 277)
z.add(power[4] * power[5] + power[6] + power[7] == 4919)
z.add(power[8] + power[9] >= 100)
z.add((power[10] / power[11]) == 2)
z.add(power[13] * power[12] >= 1001)
z.add((power[15] + power[14]) * 5 >= 201)
z.add(power[16] - power[17] == 42)
z.add(power[18] - power[19] == 2)
z.add(power[21] * power[20] >= 900)


# Check if  z3 can solve the problem
if str(z.check()) == "sat":
    print("z3 can solve it")

    # now we can create the model and evaluate it to "real" values
    model = z.model()
    solution = ""
    for f in power:
        solution += chr(int(model.evaluate(f).as_long()))
        print(solution)
else:
    print("unsolvable")
```

```
z3 can solve it
R
R6
R6@
R6@M
R6@Mb
R6@Mb1
R6@Mb1@
R6@Mb1@5
R6@Mb1@5_
R6@Mb1@5_N
R6@Mb1@5_Nz
R6@Mb1@5_Nz)
R6@Mb1@5_Nz)g
R6@Mb1@5_Nz)ge
R6@Mb1@5_Nz)ge'
R6@Mb1@5_Nz)ge':
R6@Mb1@5_Nz)ge':|
R6@Mb1@5_Nz)ge':|R
R6@Mb1@5_Nz)ge':|R\
R6@Mb1@5_Nz)ge':|R\Z
R6@Mb1@5_Nz)ge':|R\ZF
R6@Mb1@5_Nz)ge':|R\ZFP
```

I'm running it locally. If you run it on the server, it will give you the flag!

![image](https://user-images.githubusercontent.com/7328587/165010786-8765229a-5bdc-498b-b41d-c22030f85ba7.png)
