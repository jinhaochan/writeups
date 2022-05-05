REV - Dyn
---

Running the code, we see that it gets the flag as an input

![image](https://user-images.githubusercontent.com/7328587/166859238-a2e20e02-63d7-4b18-875e-ddeb4c4141ca.png)

Stepping through the code, it tries to `unwrap` 32 characters, which tells us that there are 32 characters between `actf{` and `}`

![image](https://user-images.githubusercontent.com/7328587/166859380-cdda0172-3a40-443e-aba4-59dff48179c4.png)

We put in some jumbled input to see where the program takes us

![image](https://user-images.githubusercontent.com/7328587/166859418-a6995116-7127-41eb-9d8c-736904b6878a.png)

The code reaches a part where it does a comparison with our jumbled input with a certain strings

![image](https://user-images.githubusercontent.com/7328587/166859462-67eb6553-d851-4eda-ad79-28f990eee68d.png)

![image](https://user-images.githubusercontent.com/7328587/166859483-620d4b5f-0e87-4762-85a9-ae78049f46eb.png)

![image](https://user-images.githubusercontent.com/7328587/166859506-4ba4b632-8327-4aad-8e18-6fb987b57a16.png)

We simply map back the jumbled index with our input, and reorder the string it's checking against to get the correct flag

```
input_str = list("qwertyuiopasdfghjkl;zxcvbnm,./!@")

jumbled_str = list("ytiuwqrefdhgposaxzvckj;l/.@!nb,m")

jumbled_flag = list("_ynourtsd_tet_eh2_bfiasl7cedbda7")

flag = ""

for c in input_str:
    index = jumbled_str.index(c)
    flag += jumbled_flag[index]

print(flag)
```

![image](https://user-images.githubusercontent.com/7328587/166859554-23f689e5-78f8-44f6-afd5-914b35beff77.png)


REV - Imposter
---

This program tries to allocate way too much memory. We just need to keep that in check and it will print the flag

The two offending instructions are:

![image](https://user-images.githubusercontent.com/7328587/166859905-e06bca50-9550-4932-828f-140a9a9e1b80.png)

![image](https://user-images.githubusercontent.com/7328587/166859916-89bc490f-454d-461c-90b2-c3213585a83b.png)

We reduce the malloc to a smaller size, and remove the `shl [rbp+size], 1]` to `shl rax, 0`, which essentially does nothing

![image](https://user-images.githubusercontent.com/7328587/166860038-7187c8da-76f2-484a-8ab8-77ba5461046f.png)

![image](https://user-images.githubusercontent.com/7328587/166860047-51030351-436e-453c-b28c-d95c9ef53f11.png)

However, by doing that, we get a new error `realloc(): invalid next size`, which means that the amount of memory rellocated is too small for the new length of the string

![image](https://user-images.githubusercontent.com/7328587/166860088-f336cc22-e22f-4878-8400-1959885e768f.png)


So we push the numbers slightly higher to `100h` and we see that some strings are being printed out which roughly resembles the flag

![image](https://user-images.githubusercontent.com/7328587/166860319-c31943ae-b7de-4785-adfd-7182fddce484.png)

![image](https://user-images.githubusercontent.com/7328587/166860346-02bc7e7d-f65c-4f6a-b2c9-d02970f5554e.png)

![image](https://user-images.githubusercontent.com/7328587/166860364-19ce0db1-c704-4819-958d-fd7d23a5117a.png)

Through trial and error, setting it above `200h` prints out the entire flag

![image](https://user-images.githubusercontent.com/7328587/166860424-3ace71c9-1ecf-44e9-8c92-e5e44999d578.png)


