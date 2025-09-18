## Docker Multistage Builds



Docker multi-stage build is a feature that allows you to use multiple stages inside one Dockerfile. Each stage can have its own base image and tools. The main idea is to build your application in one stage (with all heavy dependencies like compilers, SDKs, etc.) and then copy only the necessary output into a final lightweight image. In Multi Stage Each Stage is an layer .

**This helps to:**

- Reduce image size 

- Improve security - (no build tools in final image)

- Make Dockerfiles easier to manage

` In simple terms: You use one stage to build, another to run.`


```
$ docker build --platform linux/amd64 -t streamlit-app .  # By Default it will use the Dockerfile Instructions
```
```
$ docker build --platform linux/amd64 -t streamlit-app1 -f Dockerfile.Multi_stage .
```

**Result :**
```
Image size of streamlit-app = 46.6 MB
Image size of streamlit-app1 = 43.3 MB
```

**Note :**

You can see the Second Image reduced by 3 MB but if you try this in bigger application you can see a significant size reduction . 