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
