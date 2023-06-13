# LearnOpenGL

---

## 你好,窗口!

```cpp
#include <glad/glad.h>//必须要先include glad
#include <GLFW/glfw3.h>

#define WIDTH  800//定义窗体和视口初始的宽度
#define HEIGHT 600//定义窗体和视口初始的高度

/*当用户调整窗口的尺寸的同时必须要调整视口的尺寸与之对应,不然不会出现合适的渲染结果*/
void frambuffer_size_callback(GLFWwindow* window, int width, int height)
{
    glViewport(0,0,width,height);
}
/*判断用户是否按下esc键,如果按下则setwindowshouldclose*/
void ProcessInput(GLFWwindow* window)
{
    if(glfwGetKey(window, GLFW_KEY_ESCAPE)){
        glfwSetWindowShouldClose(window, true);
    }
}

int main()
{
    glfwInit();//初始化glfw库,GLFW 的主要功能是创建并管理窗口和 OpenGL 上下文
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);//设置glfw的版本号3.3(major)
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);//设置glfw的版本号3.3(minor)
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);//设置核心模式,不兼容旧函数
    GLFWwindow* window = glfwCreateWindow(WIDTH,HEIGHT,"just try", NULL, NULL);//创建一个窗口
    glfwMakeContextCurrent(window);//将window窗口设置为Current
    gladLoadGLLoader((GLADloadproc)glfwGetProcAddress);//初始化GLAD,GLAD的作用是管理OpenGL的函数指针
    glViewport(0, 0, WIDTH, HEIGHT);//告诉OpenGL视口的尺寸(像素的个数为WIDTH * HEIGHT)
    glfwSetFramebufferSizeCallback(window, frambuffer_size_callback);//注册回调函数framebuffer_size_callback

    //Render loop
    while(!glfwWindowShouldClose(window))
    {
        //输入------------------------------
        ProcessInput(window);

        //渲染指令--------------------------
        glClearColor(0.2f,0.3f,0.3f,1.0f);//设置用什么颜色清空屏幕
        glClear(GL_COLOR_BUFFER_BIT);//清楚指定的缓冲(这里是颜色缓冲)


        //检查并调用事件,交换缓冲------------

        /*检查是否触发事件(比如键盘输入,鼠标移动等),更新窗口状态,并调用回调函数*/
        glfwPollEvents();
        /**
         * 为了避免图像闪烁的问题,使用双缓冲
         * 前缓冲保存着最终输出的图像，它会在屏幕上显示；而所有的的渲染指令都会在后缓冲上绘制
         * 这个函数的作用就是交换前缓冲和后缓冲
         */
        glfwSwapBuffers(window);
    }

    glfwTerminate();//结束进程,回收内存
    return 0;
}
```



## 你好,三角形!

>- 顶点数组对象: Vertex Array Object, **VAO**
>- 顶点缓冲对象: Vertex Buffer Object, **VBO**
>- 元素缓冲对象: Element Buffer Object, **EBO**
>  - (又称)索引缓冲对象: Index Buffer Object, **IBO**

3D坐标转为2D坐标的处理过程是由OpenGL的**图形渲染管线**管理的.

图形渲染管线可以被划分为两个主要部分：第一部分把你的3D坐标转换为2D坐标，第二部分是把2D坐标转变为实际的有颜色的像素。

> **2D坐标和像素也是不同的**，2D坐标精确表示一个点在2D空间中的位置，而2D像素是这个点的近似值，2D像素受到你的屏幕/窗口分辨率的限制。

OpenGL**着色器**是用OpenGL着色器语言(OpenGL Shading Language, **GLSL**)写成的

下面，你会看到一个图形渲染管线的每个阶段的抽象展示。要注意蓝色部分代表的是我们可以注入自定义的着色器的部分。

![img](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202304141606042.png)



> 以下的内容十分重要,需要反复记忆,在此我不作提炼



如你所见，图形渲染管线包含很多部分，每个部分都将在转换顶点数据到最终像素这一过程中处理各自特定的阶段。我们会概括性地解释一下渲染管线的每个部分，让你对图形渲染管线的工作方式有个大概了解。

### 顶点属性

首先，我们以数组的形式传递3个3D坐标作为图形渲染管线的输入，用来表示一个三角形，这个数组叫做顶点数据(Vertex Data)；顶点数据是一系列顶点的集合。一个顶点(Vertex)是一个3D坐标的数据的集合。而顶点数据是用**顶点属性**(Vertex Attribute)表示的，它可以包含任何我们想用的数据，但是简单起见，我们还是假定每个顶点只由一个3D位置(译注1)和一些颜色值组成的吧。

```
译注1

当我们谈论一个“位置”的时候，它代表在一个“空间”中所处地点的这个特殊属性；同时“空间”代表着任何一种坐标系，比如x、y、z三维坐标系，x、y二维坐标系，或者一条直线上的x和y的线性关系，只不过二维坐标系是一个扁扁的平面空间，而一条直线是一个很瘦的长长的空间。
```

```
为了让OpenGL知道我们的坐标和颜色值构成的到底是什么，OpenGL需要你去指定这些数据所表示的渲染类型。我们是希望把这些数据渲染成一系列的点？一系列的三角形？还是仅仅是一个长长的线？做出的这些提示叫做图元(Primitive)，任何一个绘制指令的调用都将把图元传递给OpenGL。这是其中的几个：GL_POINTS、GL_TRIANGLES、GL_LINE_STRIP。
```

```cpp
float vertices[] = {
    -0.5f, -0.5f, 0.0f,
    0.5f, -0.5f, 0.0f,
    0.0f, 0.5f, 0.0f
};

unsigned int VBO;
glGenBuffers(1, &VBO);//用一个缓冲ID(1)生成了一个VBO对象
glBindBuffer(GL_ARRAY_BUFFER, VBO);//把新创建的缓冲绑定到GL_ARRAY_BUFFER目标上
/**
 * GL_STATIC_DRAW ：数据不会或几乎不会改变
 * GL_DYNAMIC_DRAW：数据会被改变很多
 * GL_STREAM_DRAW ：数据每次绘制时都会改变
 */
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
```

### 顶点着色器

图形渲染管线的第一个部分是**顶点着色器**(Vertex Shader)，它把一个单独的顶点作为输入。顶点着色器主要的目的是把3D坐标转为另一种3D坐标（后面会解释），同时顶点着色器允许我们对顶点属性进行一些基本处理。

```cpp
const char *vertexShaderSource = "#version 330 core\n"
"layout (location = 0) in vec3 aPos;\n"
"void main()\n"
"{\n"
"   gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n"
"}\0";
unsigned int vertexShader;
vertexShader = glCreateShader(GL_VERTEX_SHADER);
glShaderSource(vertexShader, 1, &vertexShaderSource, NULL);
glCompileShader(vertexShader);
```



### 图元装配

**图元装配**(Primitive Assembly)阶段将顶点着色器输出的所有顶点作为输入（如果是GL_POINTS，那么就是一个顶点），并所有的点装配成指定图元的形状；本节例子中是一个三角形。

### 几何着色器

图元装配阶段的输出会传递给**几何着色器**(Geometry Shader)。几何着色器把图元形式的一系列顶点的集合作为输入，它可以通过产生新顶点构造出新的（或是其它的）图元来生成其他形状。例子中，它生成了另一个三角形。

### 光栅化阶段

几何着色器的输出会被传入**光栅化阶段**(Rasterization Stage)，这里它会把图元映射为最终屏幕上相应的像素，生成供片段着色器(Fragment Shader)使用的片段(Fragment)。在片段着色器运行之前会执行**裁切**(Clipping)。裁切会丢弃超出你的视图以外的所有像素，用来提升执行效率。

```
OpenGL中的一个片段是OpenGL渲染一个像素所需的所有数据。
```

### 片段着色器

**片段着色器**的主要目的是计算一个像素的最终颜色，这也是所有OpenGL高级效果产生的地方。通常，片段着色器包含3D场景的数据（比如光照、阴影、光的颜色等等），这些数据可以被用来计算最终像素的颜色。

```cpp
const char *fragmentShaderSource = "#version 330 core\n"
"out vec4 FragColor;\n"
"void main()\n"
"{\n"
"   FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f);\n"
"}\n";
unsigned int fragmentShader;
fragmentShader = glCreateShader(GL_FRAGMENT_SHADER);
glShaderSource(fragmentShader, 1, &fragmentShaderSource, NULL);
glCompileShader(fragmentShader);
```



### Alpha测试和混合(Blending)

在所有对应颜色值确定以后，最终的对象将会被传到最后一个阶段，我们叫做**Alpha测试和混合(Blending)**阶段。这个阶段检测片段的对应的深度（和模板(Stencil)）值（后面会讲），用它们来判断这个像素是其它物体的前面还是后面，决定是否应该丢弃。这个阶段也会检查**alpha**值（alpha值定义了一个物体的透明度）并对物体进行**混合**(Blend)。所以，即使在片段着色器中计算出来了一个像素输出的颜色，在渲染多个三角形的时候最后的像素颜色也可能完全不同。



可以看到，图形渲染管线非常复杂，它包含很多可配置的部分。然而，对于大多数场合，我们只需要配置顶点和片段着色器就行了。几何着色器是可选的，通常使用它默认的着色器就行了。

在现代OpenGL中，我们**必须**定义至少一个顶点着色器和一个片段着色器（因为GPU中没有默认的顶点/片段着色器）。出于这个原因，刚开始学习现代OpenGL的时候可能会非常困难，因为在你能够渲染自己的第一个三角形之前已经需要了解一大堆知识了。在本节结束你最终渲染出你的三角形的时候，你也会了解到非常多的图形编程知识。

### 着色器程序

在写好了各个着色器并编译之后,接下来的步骤就是链接了

```cpp
unsigned int shaderProgram;
shaderProgram = glCreateProgram();
glAttachShader(shaderProgram, vertexShader);
glAttachShader(shaderProgram, fragmentShader);
glLinkProgram(shaderProgram);
```

到的结果就是一个程序对象，我们可以调用glUseProgram函数，用刚创建的程序对象作为它的参数，以激活这个程序对象：

```
glUseProgram(shaderProgram);
```

在glUseProgram函数调用之后，每个着色器调用和渲染调用都会使用这个程序对象（也就是之前写的着色器)了。

对了，在把着色器对象链接到程序对象以后，记得删除着色器对象，我们不再需要它们了：

```
glDeleteShader(vertexShader);
glDeleteShader(fragmentShader);
```

现在，我们已经把输入顶点数据发送给了GPU，并指示了GPU如何在顶点和片段着色器中处理它。就快要完成了，但还没结束，OpenGL还不知道它该如何解释内存中的顶点数据，以及它该如何将顶点数据链接到顶点着色器的属性上。我们需要告诉OpenGL怎么做。

### 链接顶点属性

```cpp
unsigned int VBO, VAO;
glGenVertexArrays(1, &VAO);
glGenBuffers(1, &VBO);
// bind the Vertex Array Object first, then bind and set vertex buffer(s), and then configure vertex attributes(s).
glBindVertexArray(VAO);

glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);

// note that this is allowed, the call to glVertexAttribPointer registered VBO as the vertex attribute's bound vertex buffer object so afterwards we can safely unbind
glBindBuffer(GL_ARRAY_BUFFER, 0); 

// You can unbind the VAO afterwards so other VAO calls won't accidentally modify this VAO, but this rarely happens. Modifying other
// VAOs requires a call to glBindVertexArray anyways so we generally don't unbind VAOs (nor VBOs) when it's not directly necessary.
glBindVertexArray(0); 
```

### 源代码

完整代码如下:

```cpp
#include <glad/glad.h>//必须要先include glad
#include <GLFW/glfw3.h>
#include <iostream>
#define WIDTH  800//定义窗体和视口初始的宽度
#define HEIGHT 600//定义窗体和视口初始的高度

/*当用户调整窗口的尺寸的同时必须要调整视口的尺寸与之对应,不然不会出现合适的渲染结果*/
void frambuffer_size_callback(GLFWwindow* window, int width, int height)
{
glViewport(0,0,width,height);
}
/*判断用户是否按下esc键,如果按下则setwindowshouldclose*/
void ProcessInput(GLFWwindow* window)
{
if(glfwGetKey(window, GLFW_KEY_ESCAPE)){
    glfwSetWindowShouldClose(window, true);
}
}

int main()
{
glfwInit();//初始化glfw库,GLFW 的主要功能是创建并管理窗口和 OpenGL 上下文
glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);//设置glfw的版本号3.3(major)
glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);//设置glfw的版本号3.3(minor)
glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);//设置核心模式,不兼容旧函数
GLFWwindow* window = glfwCreateWindow(WIDTH,HEIGHT,"just try", NULL, NULL);//创建一个窗口
glfwMakeContextCurrent(window);//将window窗口设置为Current
gladLoadGLLoader((GLADloadproc)glfwGetProcAddress);//初始化GLAD,GLAD的作用是管理OpenGL的函数指针
glViewport(0, 0, WIDTH, HEIGHT);//告诉OpenGL视口的尺寸(像素的个数为WIDTH * HEIGHT)
glfwSetFramebufferSizeCallback(window, frambuffer_size_callback);//注册回调函数framebuffer_size_callback


float vertices[] = {
    -0.5f, -0.5f, 0.0f,
    0.5f, -0.5f, 0.0f,
    0.0f, 0.5f, 0.0f
};




const char *vertexShaderSource = "#version 330 core\n"
"layout (location = 0) in vec3 aPos;\n"
"void main()\n"
"{\n"
"   gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n"
"}\0";
unsigned int vertexShader;
vertexShader = glCreateShader(GL_VERTEX_SHADER);
glShaderSource(vertexShader, 1, &vertexShaderSource, NULL);
glCompileShader(vertexShader);

const char *fragmentShaderSource = "#version 330 core\n"
"out vec4 FragColor;\n"
"void main()\n"
"{\n"
"   FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f);\n"
"}\n\0";

unsigned int fragmentShader;
fragmentShader = glCreateShader(GL_FRAGMENT_SHADER);
glShaderSource(fragmentShader, 1, &fragmentShaderSource, NULL);
glCompileShader(fragmentShader);


unsigned int shaderProgram;
shaderProgram = glCreateProgram();
glAttachShader(shaderProgram, vertexShader);
glAttachShader(shaderProgram, fragmentShader);
glLinkProgram(shaderProgram);

glDeleteShader(vertexShader);
glDeleteShader(fragmentShader);

unsigned int VBO, VAO;
glGenVertexArrays(1, &VAO);
glGenBuffers(1, &VBO);
// bind the Vertex Array Object first, then bind and set vertex buffer(s), and then configure vertex attributes(s).
glBindVertexArray(VAO);

glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);

// note that this is allowed, the call to glVertexAttribPointer registered VBO as the vertex attribute's bound vertex buffer object so afterwards we can safely unbind
glBindBuffer(GL_ARRAY_BUFFER, 0); 

// You can unbind the VAO afterwards so other VAO calls won't accidentally modify this VAO, but this rarely happens. Modifying other
// VAOs requires a call to glBindVertexArray anyways so we generally don't unbind VAOs (nor VBOs) when it's not directly necessary.
glBindVertexArray(0); 

//Render loop
while(!glfwWindowShouldClose(window))
{
    //输入------------------------------
    ProcessInput(window);

    //渲染指令--------------------------
    glClearColor(0.2f,0.3f,0.3f,1.0f);//设置用什么颜色清空屏幕
    glClear(GL_COLOR_BUFFER_BIT);//清楚指定的缓冲(这里是颜色缓冲)

    glUseProgram(shaderProgram);
    glBindVertexArray(VAO);
    glDrawArrays(GL_TRIANGLES, 0, 3);

    //检查并调用事件,交换缓冲------------

    /**
     * 为了避免图像闪烁的问题,使用双缓冲
     * 前缓冲保存着最终输出的图像，它会在屏幕上显示；而所有的的渲染指令都会在后缓冲上绘制
     * 这个函数的作用就是交换前缓冲和后缓冲
     */
    glfwSwapBuffers(window);
    /*检查是否触发事件(比如键盘输入,鼠标移动等),更新窗口状态,并调用回调函数*/
    glfwPollEvents();
}

glDeleteVertexArrays(1, &VAO);
glDeleteBuffers(1, &VBO);
glDeleteProgram(shaderProgram);

glfwTerminate();//结束进程,回收内存
return 0;
}
```

输出结果:

![image-20230414180112044](https://raw.githubusercontent.com/Tianjiangyigeyi/img/master/202304141801112.png)



### 元素缓冲对象

```cpp
#include <glad/glad.h>//必须要先include glad
#include <GLFW/glfw3.h>
#include <iostream>
#define WIDTH  800//定义窗体和视口初始的宽度
#define HEIGHT 600//定义窗体和视口初始的高度

/*当用户调整窗口的尺寸的同时必须要调整视口的尺寸与之对应,不然不会出现合适的渲染结果*/
void frambuffer_size_callback(GLFWwindow* window, int width, int height)
{
glViewport(0,0,width,height);
}
/*判断用户是否按下esc键,如果按下则setwindowshouldclose*/
void ProcessInput(GLFWwindow* window)
{
if(glfwGetKey(window, GLFW_KEY_ESCAPE)){
    glfwSetWindowShouldClose(window, true);
}
}

int main()
{
glfwInit();//初始化glfw库,GLFW 的主要功能是创建并管理窗口和 OpenGL 上下文
glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);//设置glfw的版本号3.3(major)
glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);//设置glfw的版本号3.3(minor)
glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);//设置核心模式,不兼容旧函数
GLFWwindow* window = glfwCreateWindow(WIDTH,HEIGHT,"just try", NULL, NULL);//创建一个窗口
glfwMakeContextCurrent(window);//将window窗口设置为Current
gladLoadGLLoader((GLADloadproc)glfwGetProcAddress);//初始化GLAD,GLAD的作用是管理OpenGL的函数指针
glViewport(0, 0, WIDTH, HEIGHT);//告诉OpenGL视口的尺寸(像素的个数为WIDTH * HEIGHT)
glfwSetFramebufferSizeCallback(window, frambuffer_size_callback);//注册回调函数framebuffer_size_callback


float vertices[] = {
        0.5f,  0.5f, 0.0f,  // top right
        0.5f, -0.5f, 0.0f,  // bottom right
    -0.5f, -0.5f, 0.0f,  // bottom left
    -0.5f,  0.5f, 0.0f   // top left 
};
unsigned int indices[] = {  // note that we start from 0!
    0, 1, 3,  // first Triangle
    1, 2, 3   // second Triangle
};




const char *vertexShaderSource = "#version 330 core\n"
"layout (location = 0) in vec3 aPos;\n"
"void main()\n"
"{\n"
"   gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n"
"}\0";
unsigned int vertexShader;
vertexShader = glCreateShader(GL_VERTEX_SHADER);
glShaderSource(vertexShader, 1, &vertexShaderSource, NULL);
glCompileShader(vertexShader);

const char *fragmentShaderSource = "#version 330 core\n"
"out vec4 FragColor;\n"
"void main()\n"
"{\n"
"   FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f);\n"
"}\n\0";

unsigned int fragmentShader;
fragmentShader = glCreateShader(GL_FRAGMENT_SHADER);
glShaderSource(fragmentShader, 1, &fragmentShaderSource, NULL);
glCompileShader(fragmentShader);


unsigned int shaderProgram;
shaderProgram = glCreateProgram();
glAttachShader(shaderProgram, vertexShader);
glAttachShader(shaderProgram, fragmentShader);
glLinkProgram(shaderProgram);

glDeleteShader(vertexShader);
glDeleteShader(fragmentShader);

unsigned int VBO, VAO, EBO;
glGenVertexArrays(1, &VAO);
glGenBuffers(1, &VBO);
glGenBuffers(1, &EBO);
// bind the Vertex Array Object first, then bind and set vertex buffer(s), and then configure vertex attributes(s).
glBindVertexArray(VAO);

glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);

glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);

// note that this is allowed, the call to glVertexAttribPointer registered VBO as the vertex attribute's bound vertex buffer object so afterwards we can safely unbind
glBindBuffer(GL_ARRAY_BUFFER, 0); 

// You can unbind the VAO afterwards so other VAO calls won't accidentally modify this VAO, but this rarely happens. Modifying other
// VAOs requires a call to glBindVertexArray anyways so we generally don't unbind VAOs (nor VBOs) when it's not directly necessary.
glBindVertexArray(0); 

// glPolygonMode(GL_FRONT_AND_BACK, GL_LINE);

//Render loop
while(!glfwWindowShouldClose(window))
{
    //输入------------------------------
    ProcessInput(window);

    //渲染指令--------------------------
    glClearColor(0.2f,0.3f,0.3f,1.0f);//设置用什么颜色清空屏幕
    glClear(GL_COLOR_BUFFER_BIT);//清楚指定的缓冲(这里是颜色缓冲)

    glUseProgram(shaderProgram);
    glBindVertexArray(VAO);
    glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);

    //检查并调用事件,交换缓冲------------

    /**
     * 为了避免图像闪烁的问题,使用双缓冲
     * 前缓冲保存着最终输出的图像，它会在屏幕上显示；而所有的的渲染指令都会在后缓冲上绘制
     * 这个函数的作用就是交换前缓冲和后缓冲
     */
    glfwSwapBuffers(window);
    /*检查是否触发事件(比如键盘输入,鼠标移动等),更新窗口状态,并调用回调函数*/
    glfwPollEvents();
}

glDeleteVertexArrays(1, &VAO);
glDeleteBuffers(1, &VBO);
glDeleteBuffers(1, &EBO);
glDeleteProgram(shaderProgram);

glfwTerminate();//结束进程,回收内存
return 0;
}
```



