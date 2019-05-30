---
layout: post
title: 计算机图形学作业（ 三）：使用openGL画一个立方体，并实现平移、旋转和放缩变换
date: 2019-04-09 00:00:00
categories: 
- CG-计算机图形学
tags: 
- 计算机图形学
- ImGUI
- OpenGL
- C++
description: 使用openGL画一个立方体，并实现平移、旋转和放缩变换。
---



# 题目	{#problem}

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-04/2019-04-09-1.png)

# 引入GLM库  {#inport-lib}
利用 openGL 进行 3D 绘图需要用到大量的数学矩阵运算，而 OpenGL 没有自带任何的矩阵和向量知识，需要我们自己定义数学类和函数，这相对比较麻烦。所以我们需要引入 GLM 库，GLM 能快速帮助我们实现各种数学矩阵运算。

前往 [GLM官方github仓库](https://github.com/g-truc/glm/tags)，选择0.9.8.5版本，下载该版本的 glm-0.9.8.5.zip 或 glm-0.9.8.5.zip 压缩包，解压之后，在项目里引用以下文件即可：
```
#include <glm/glm.hpp>
#include <glm/gtc/matrix_transform.hpp>
#include <glm/gtc/type_ptr.hpp>
```

# 画立方体	{#paint-cube}
## 模型、观察和投影  {#model-view-projection}
利用 openGL 进行 3D 绘图时，首先要定义一个模型矩阵 model，这个矩阵包含了对 3D 物体的位移、缩放与旋转操作。这个模型矩阵创建如下：
```
glm::mat4 model;
model = glm::rotate(model, glm::radians(-55.0f), glm::vec3(1.0f, 0.0f, 0.0f));
```
然后，我们需要创建一个观察矩阵，因为我们创建的 3D 物体默认在世界坐标的原点(0, 0, 0)，而摄像机的位置也是(0, 0, 0)，所以，为了能看清楚3D物体的全貌，我们必须向前移动整个场景，这就是观察矩阵的作用。

在 openGL 中，世界坐标系的坐标表示为(x, y, z)，分别对应下图位置：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-04/2019-04-09-2.png)

注意，将摄像机向后移动，和将整个场景向前移动是一样的。这里我们将场景向 z 轴的负方向移动，以便于摄像机能观察到物体。
```
glm::mat4 view;
view = glm::translate(view, glm::vec3(0.0f, 0.0f, -3.0f));
```
最后我们需要做的是定义一个投影矩阵。我们希望在场景中使用透视投影，所以像这样声明一个投影矩阵：
```
glm::mat4 projection;
projection = glm::perspective(glm::radians(45.0f), screenWidth / screenHeight, 0.1f, 100.0f);
```

## 修改着色器	 {#rewrite-shader}
我们创建了模型、观察和投影矩阵，应该把它们传入着色器，着色器的修改如下：
```
const char* vertexShaderSource = "#version 330 core\n"
	"layout (location = 0) in vec3 aPos;\n"
	"layout (location = 1) in vec3 aColor;\n"
	"out vec3 ourColor;\n"
	"uniform mat4 model;\n"
	"uniform mat4 view;\n"
	"uniform mat4 projection;\n"
	"void main()\n"
	"{\n"
	"gl_Position = projection * view * model * vec4(aPos, 1.0);\n"
	"ourColor = aColor;\n"
	"}\0";
```
然后再程序中获取 uniform 变量，并赋值
```
//获取着色器程序uniform
unsigned int modelLoc = glGetUniformLocation(shaderProgram, "model");
unsigned int viewLoc = glGetUniformLocation(shaderProgram, "view");
unsigned int projectionLoc = glGetUniformLocation(shaderProgram, "projection");
glUniformMatrix4fv(modelLoc, 1, GL_FALSE, glm::value_ptr(model));
glUniformMatrix4fv(viewLoc, 1, GL_FALSE, &view[0][0]);
glUniformMatrix4fv(projectionLoc, 1, GL_FALSE, &projection[0][0]);
```

## 立方体的顶点	 {#cube-vertices}
要想渲染一个立方体，我们一共需要36个顶点（6个面 x 每个面有2个三角形组成 x 每个三角形有3个顶点），顶点如下：
```
float vertices[] = {
    -0.5f, -0.5f, -0.5f,  0.0f, 0.0f,
     0.5f, -0.5f, -0.5f,  1.0f, 0.0f,
     0.5f,  0.5f, -0.5f,  1.0f, 1.0f,
     0.5f,  0.5f, -0.5f,  1.0f, 1.0f,
    -0.5f,  0.5f, -0.5f,  0.0f, 1.0f,
    -0.5f, -0.5f, -0.5f,  0.0f, 0.0f,

    -0.5f, -0.5f,  0.5f,  0.0f, 0.0f,
     0.5f, -0.5f,  0.5f,  1.0f, 0.0f,
     0.5f,  0.5f,  0.5f,  1.0f, 1.0f,
     0.5f,  0.5f,  0.5f,  1.0f, 1.0f,
    -0.5f,  0.5f,  0.5f,  0.0f, 1.0f,
    -0.5f, -0.5f,  0.5f,  0.0f, 0.0f,

    -0.5f,  0.5f,  0.5f,  1.0f, 0.0f,
    -0.5f,  0.5f, -0.5f,  1.0f, 1.0f,
    -0.5f, -0.5f, -0.5f,  0.0f, 1.0f,
    -0.5f, -0.5f, -0.5f,  0.0f, 1.0f,
    -0.5f, -0.5f,  0.5f,  0.0f, 0.0f,
    -0.5f,  0.5f,  0.5f,  1.0f, 0.0f,

     0.5f,  0.5f,  0.5f,  1.0f, 0.0f,
     0.5f,  0.5f, -0.5f,  1.0f, 1.0f,
     0.5f, -0.5f, -0.5f,  0.0f, 1.0f,
     0.5f, -0.5f, -0.5f,  0.0f, 1.0f,
     0.5f, -0.5f,  0.5f,  0.0f, 0.0f,
     0.5f,  0.5f,  0.5f,  1.0f, 0.0f,

    -0.5f, -0.5f, -0.5f,  0.0f, 1.0f,
     0.5f, -0.5f, -0.5f,  1.0f, 1.0f,
     0.5f, -0.5f,  0.5f,  1.0f, 0.0f,
     0.5f, -0.5f,  0.5f,  1.0f, 0.0f,
    -0.5f, -0.5f,  0.5f,  0.0f, 0.0f,
    -0.5f, -0.5f, -0.5f,  0.0f, 1.0f,

    -0.5f,  0.5f, -0.5f,  0.0f, 1.0f,
     0.5f,  0.5f, -0.5f,  1.0f, 1.0f,
     0.5f,  0.5f,  0.5f,  1.0f, 0.0f,
     0.5f,  0.5f,  0.5f,  1.0f, 0.0f,
    -0.5f,  0.5f,  0.5f,  0.0f, 0.0f,
    -0.5f,  0.5f, -0.5f,  0.0f, 1.0f
};
```
由于题目要求立方体边长为4，所以上述代码中，把0.5全部换成2即可。由于立方体边长增大，最终可能无法看清立方体全貌，所以需要把场景再往前移动，即观察矩阵中，增加场景向 z 轴的负方向移动的距离。

## 深度测试  {#depth-test}
完成之前的步骤，便可以画出一个立方体，如下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-04/2019-04-09-3.png)

但可以看到，立方体的某些本应被遮挡住的面被绘制在了这个立方体其他面之上。之所以这样是因为 OpenGL 是一个三角形一个三角形地来绘制你的立方体的，所以即便之前那里有东西它也会覆盖之前的像素。因为这个原因，有些三角形会被绘制在其它三角形上面，虽然它们本不应该是被覆盖的。

为了解决这个问题，我们需要开启深度测试。OpenGL 存储图形的所有深度信息于一个 Z 缓冲中，也被称为深度缓冲。GLFW 会自动为你生成这样一个缓冲。深度值存储在每个片段里面，当片段想要输出它的颜色时，OpenGL 会将它的深度值和 z 缓冲进行比较，如果当前的片段在其它片段之后，它将会被丢弃，否则将会覆盖。

使用以下命令开启深度测试：
```
glEnable(GL_DEPTH_TEST);
```
最终的结果如下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-04/2019-04-09-4.png)

# 立方体变换  {#cube-transform}
## 平移  {#translate}
使用以下函数实现平移：
```
model = glm::translate(model, glm::vec3(posDelta, 0, 0));
```
该函数的第一个参数就是之前创建的模型矩阵 model，第二个参数是个三维向量，分别对应 x、y、z 的位置。我在代码中通过自增和自减改变 posDelta 的值，从而实现立方体在 x 轴上来回移动。

## 旋转  {#rotate}
使用以下函数实现旋转：
```
model = glm::rotate(model, rotateAngle, glm::vec3(1.0f, 0.0f, 1.0f));
```
该函数的第一个参数就是之前创建的模型矩阵 model，第二个参数是旋转的弧度，这里我利用弧度的周期为 2pi，让变量 rotate 不断自增，实现立方体不断旋转。函数的第三个参数是旋转的轴，这里选择了 xoz 平面上 x=z 这条轴。
 
## 放缩  {#scale}
使用以下函数实现放缩：
```
model = glm::scale(model, glm::vec3(scaleDelta, scaleDelta, scaleDelta));
```
该函数的第一个参数就是之前创建的模型矩阵 model，第二个参数是个三位向量，对应 x、y、z三个方向放缩的倍数。

# 渲染管线的理解  {#about}
下图可以抽象地展示出图形渲染管线的各个步骤：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-04/2019-04-09-5.png)

首先，顶点数据是一系列顶点的集合。一个顶点是一个3D坐标的数据的集合。而顶点数据是用顶点属性表示的，它可以包含任何我们想用的数据。

图形渲染管线的第一个部分是顶点着色器，它把一个单独的顶点作为输入。顶点着色器主要的目的是把3D坐标转为另一种3D坐标，同时顶点着色器允许我们对顶点属性进行一些基本处理。

图元装配阶段将顶点着色器输出的所有顶点作为输入，并所有的点装配成指定图元的形状。

几何着色器把图元形式的一系列顶点的集合作为输入，它可以通过产生新顶点构造出新的图元来生成其他形状。

光栅化阶段会把图元映射为最终屏幕上相应的像素，生成供片段着色器使用的片段。在片段着色器运行之前会执行裁切。裁切会丢弃超出你的视图以外的所有像素，用来提升执行效率。

片段着色器的主要目的是计算一个像素的最终颜色，这也是所有 OpenGL 高级效果产生的地方。通常，片段着色器包含3D场景的数据（比如光照、阴影、光的颜色等等），这些数据可以被用来计算最终像素的颜色。

Alpha测试和混合阶段。这个阶段检测片段的对应的深度值，用它们来判断这个像素是其它物体的前面还是后面，决定是否应该丢弃。这个阶段也会检查alpha值（alpha值定义了一个物体的透明度）并对物体进行混合。

# 代码  {#code}
```
#include <iostream>
#include <glad/glad.h>
#include <GLFW/glfw3.h>
#include "imgui.h"
#include "imgui_impl_glfw.h"
#include "imgui_impl_opengl3.h"
#include <glm.hpp>
#include <gtc/matrix_transform.hpp>
#include <gtc/type_ptr.hpp>

using namespace std;

const int WINDOW_WIDTH = 1000;
const int WINDOW_HEIGHT = 800;

void framebuffer_size_callback(GLFWwindow* window, int width, int height);
void processInput(GLFWwindow* window);

const char* vertexShaderSource = "#version 330 core\n"
	"layout (location = 0) in vec3 aPos;\n"
	"layout (location = 1) in vec3 aColor;\n"
	"out vec3 ourColor;\n"
	"uniform mat4 model;\n"
	"uniform mat4 view;\n"
	"uniform mat4 projection;\n"
	"void main()\n"
	"{\n"
	"gl_Position = projection * view * model * vec4(aPos, 1.0);\n"
	"ourColor = aColor;\n"
	"}\0";

//片段着色器
const char* fragmentShaderSource = "#version 330 core\n"
	"out vec4 FragColor;\n"
	"in vec3 ourColor;\n"
	"void main()\n"
	"{\n"
	"FragColor = vec4(ourColor, 1.0);\n"
	"}\0";

int main() {
	//初始化opengl窗口和配置
	glfwInit();
	glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
	glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
	glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
	
	GLFWwindow* window = glfwCreateWindow(WINDOW_WIDTH, WINDOW_HEIGHT, "LearnOpenGL", NULL, NULL);
	if (window == NULL) {
		std::cout << "Failed to create GLFW window" << std::endl;
		glfwTerminate();
		return -1;
	}
	glfwMakeContextCurrent(window);
	glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);

	if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress)) {
		std::cout << "Failed to initialize GLAD" << std::endl;
		return -1;
	}

	//创建并绑定ImGui
	const char* glsl_version = "#version 130";
	IMGUI_CHECKVERSION();
	ImGui::CreateContext();
	ImGuiIO& io = ImGui::GetIO(); (void)io;
	ImGui::StyleColorsDark();
	ImGui_ImplGlfw_InitForOpenGL(window, true);
	ImGui_ImplOpenGL3_Init(glsl_version);

	//顶点着色器
	unsigned int vertexShader;
	vertexShader = glCreateShader(GL_VERTEX_SHADER);
	glShaderSource(vertexShader, 1, &vertexShaderSource, NULL);
	glCompileShader(vertexShader);

	//片段着色器
	unsigned int fragmentShader;
	fragmentShader = glCreateShader(GL_FRAGMENT_SHADER);
	glShaderSource(fragmentShader, 1, &fragmentShaderSource, NULL);
	glCompileShader(fragmentShader);

	//着色器程序
	unsigned int shaderProgram;
	shaderProgram = glCreateProgram();
	glAttachShader(shaderProgram, vertexShader);
	glAttachShader(shaderProgram, fragmentShader);
	glLinkProgram(shaderProgram);

	glDeleteShader(vertexShader);
	glDeleteShader(fragmentShader);

	bool show_window = true;

	unsigned int VAO;
	unsigned int VBO;
	unsigned int EBO;

	//正方体边长为2.0f*2s
	float half_edge = 2.0f;

	//颜色数据
	ImVec4 color1 = ImVec4(0.5f, 0.0f, 0.0f, 1.0f);
	ImVec4 color2 = ImVec4(0.0f, 0.5f, 0.0f, 1.0f);
	ImVec4 color3 = ImVec4(0.0f, 0.0f, 0.5f, 1.0f);
	ImVec4 color4 = ImVec4(0.0f, 0.5f, 0.5f, 1.0f);
	ImVec4 color5 = ImVec4(0.5f, 0.0f, 0.5f, 1.0f);
	ImVec4 color6 = ImVec4(0.5f, 0.5f, 0.0f, 1.0f);

	float vertices[] = {
	-half_edge, -half_edge, -half_edge, color1.x, color1.y, color1.z,
	 half_edge, -half_edge, -half_edge, color1.x, color1.y, color1.z,
	 half_edge,  half_edge, -half_edge, color1.x, color1.y, color1.z,
	 half_edge,  half_edge, -half_edge, color1.x, color1.y, color1.z,
	-half_edge,  half_edge, -half_edge, color1.x, color1.y, color1.z,
	-half_edge, -half_edge, -half_edge, color1.x, color1.y, color1.z,

	-half_edge, -half_edge,  half_edge, color2.x, color2.y, color2.z,
	 half_edge, -half_edge,  half_edge, color2.x, color2.y, color2.z,
	 half_edge,  half_edge,  half_edge, color2.x, color2.y, color2.z,
	 half_edge,  half_edge,  half_edge, color2.x, color2.y, color2.z,
	-half_edge,  half_edge,  half_edge, color2.x, color2.y, color2.z,
	-half_edge, -half_edge,  half_edge, color2.x, color2.y, color2.z,

	-half_edge,  half_edge,  half_edge, color3.x, color3.y, color3.z,
	-half_edge,  half_edge, -half_edge, color3.x, color3.y, color3.z,
	-half_edge, -half_edge, -half_edge, color3.x, color3.y, color3.z,
	-half_edge, -half_edge, -half_edge, color3.x, color3.y, color3.z,
	-half_edge, -half_edge,  half_edge, color3.x, color3.y, color3.z,
	-half_edge,  half_edge,  half_edge, color3.x, color3.y, color3.z,

	 half_edge,  half_edge,  half_edge, color4.x, color4.y, color4.z,
	 half_edge,  half_edge, -half_edge, color4.x, color4.y, color4.z,
	 half_edge, -half_edge, -half_edge, color4.x, color4.y, color4.z,
	 half_edge, -half_edge, -half_edge, color4.x, color4.y, color4.z,
	 half_edge, -half_edge,  half_edge, color4.x, color4.y, color4.z,
	 half_edge,  half_edge,  half_edge, color4.x, color4.y, color4.z,

	-half_edge, -half_edge, -half_edge, color5.x, color5.y, color5.z,
	 half_edge, -half_edge, -half_edge, color5.x, color5.y, color5.z,
	 half_edge, -half_edge,  half_edge, color5.x, color5.y, color5.z,
	 half_edge, -half_edge,  half_edge, color5.x, color5.y, color5.z,
	-half_edge, -half_edge,  half_edge, color5.x, color5.y, color5.z,
	-half_edge, -half_edge, -half_edge, color5.x, color5.y, color5.z,

	-half_edge,  half_edge, -half_edge, color6.x, color6.y, color6.z,
	 half_edge,  half_edge, -half_edge, color6.x, color6.y, color6.z,
	 half_edge,  half_edge,  half_edge, color6.x, color6.y, color6.z,
	 half_edge,  half_edge,  half_edge, color6.x, color6.y, color6.z,
	-half_edge,  half_edge,  half_edge, color6.x, color6.y, color6.z,
	-half_edge,  half_edge, -half_edge, color6.x, color6.y, color6.z,
	};

	unsigned int indices[] = { // 注意索引从0开始! 
		0, 1, 2, // 第一个三角形
		3, 4, 5,

		6, 7, 8,
		9, 10, 11,
		
		12, 13, 14,
		15, 16, 17,

		18, 19, 20,
		21, 22, 23,

		24, 25, 26,
		27, 28, 29,

		30, 31, 32,
		33, 34, 35
	};

	//必须先绑定VA0
	glGenVertexArrays(1, &VAO);
	glBindVertexArray(VAO);

	//再绑定VBO
	glGenBuffers(1, &VBO);
	glBindBuffer(GL_ARRAY_BUFFER, VBO);
	glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

	//使用EBO画多个三角形，组合成其它图形
	glGenBuffers(1, &EBO);
	glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
	glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);

	//再设置属性
	//位置属性
	//属性位置值为0的顶点属性，有3个值，顶点着色器总参数大小为6个float，位置属性偏移为0
	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)0);
	glEnableVertexAttribArray(0);

	//颜色属性
	//属性位置值为0的顶点属性，有3个值，顶点着色器总参数大小为6个float，位置属性偏移为3个float
	glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)(3 * sizeof(float)));
	glEnableVertexAttribArray(1);

	bool enableDepthTest = true;
	bool enableScale = true;
	bool enableTranslate = true;
	bool enableRotate = true;
	float posDelta = 1.0f;
	bool posDeltaIncreasement = true;
	float scaleDelta = 1.0f;
	bool scaleDeltaIncreasement = true;
	float rotateAngle = 0.0f;

	while (!glfwWindowShouldClose(window)) {
		processInput(window);

		if (enableDepthTest) {
			glEnable(GL_DEPTH_TEST);
		}
		else {
			glDisable(GL_DEPTH_TEST);
		}

		if (enableTranslate) {
			if (posDeltaIncreasement) {
				posDelta = posDelta + 0.01f;
				if (posDelta >= 10) {
					posDeltaIncreasement = false;
				}
			}
			else {
				posDelta = posDelta - 0.01f;
				if (posDelta <= -10) {
					posDeltaIncreasement = true;
				}
			}
		}
		
		if (enableScale) {
			if (scaleDeltaIncreasement) {
				scaleDelta = scaleDelta + 0.001f;
				if (scaleDelta >= 1.5) {
					scaleDeltaIncreasement = false;
				}
			}
			else {
				scaleDelta = scaleDelta - 0.001f;
				if (scaleDelta <= 0.5) {
					scaleDeltaIncreasement = true;
				}
			}
		}

		if (enableRotate) {
			rotateAngle = rotateAngle + 0.002f;
		}

		//创建坐标变换
		glm::mat4 model = glm::mat4(1.0f);
		glm::mat4 view = glm::mat4(1.0f);
		glm::mat4 projection = glm::mat4(1.0f);
		//model包含了位移、缩放与旋转操作
		
		model = glm::scale(model, glm::vec3(scaleDelta, scaleDelta, scaleDelta));
		model = glm::translate(model, glm::vec3(posDelta, 0, 0));
		model = glm::rotate(model, rotateAngle, glm::vec3(1.0f, 0.0f, 1.0f));
		view = glm::translate(view, glm::vec3(0.0f, 0.0f, -40.0f));
		projection = glm::perspective(glm::radians(45.0f), (float)WINDOW_WIDTH / (float)WINDOW_HEIGHT, 0.1f, 100.0f);

		//使用着色器程序
		glUseProgram(shaderProgram);

		//获取着色器程序uniform
		unsigned int modelLoc = glGetUniformLocation(shaderProgram, "model");
		unsigned int viewLoc = glGetUniformLocation(shaderProgram, "view");
		unsigned int projectionLoc = glGetUniformLocation(shaderProgram, "projection");
		glUniformMatrix4fv(modelLoc, 1, GL_FALSE, glm::value_ptr(model));
		glUniformMatrix4fv(viewLoc, 1, GL_FALSE, &view[0][0]);
		glUniformMatrix4fv(projectionLoc, 1, GL_FALSE, &projection[0][0]);
		
		glfwPollEvents();

		//清除屏幕
		glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
		glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

		//画三角形，第二个参数代表三角形的个数 * 3
		glBindVertexArray(VAO);
		glDrawElements(GL_TRIANGLES, 36, GL_UNSIGNED_INT, 0);


		//创建imgui
		ImGui_ImplOpenGL3_NewFrame();
		ImGui_ImplGlfw_NewFrame();
		ImGui::NewFrame();

		ImGui::Begin("Edit cube", &show_window, ImGuiWindowFlags_MenuBar);
		if (ImGui::Button("enable depth test")) {
			if (enableDepthTest) {
				enableDepthTest = false;
			}
			else {
				enableDepthTest = true;
			}
		}
		if (ImGui::Button("enable scale")) {
			if (enableScale) {
				enableScale = false;
			}
			else {
				enableScale = true;
			}
		}
		if (ImGui::Button("enable translate")) {
			if (enableTranslate) {
				enableTranslate = false;
			}
			else {
				enableTranslate = true;
			}
		}
		if (ImGui::Button("enable rotate")) {
			if (enableRotate) {
				enableRotate= false;
			}
			else {
				enableRotate = true;
			}
		}
		
		ImGui::End();

		ImGui::Render();
		int display_w, display_h;
		glfwMakeContextCurrent(window);
		glfwGetFramebufferSize(window, &display_w, &display_h);
		glViewport(0, 0, display_w, display_h);
		ImGui_ImplOpenGL3_RenderDrawData(ImGui::GetDrawData());

		glfwSwapBuffers(window);
	}

	glDeleteVertexArrays(1, &VAO);
	glDeleteBuffers(1, &VBO);
	glDeleteBuffers(1, &EBO);

	glfwTerminate();

	return 0;
}

void framebuffer_size_callback(GLFWwindow* window, int width, int height) {
	glViewport(0, 0, width, height);
}

void processInput(GLFWwindow* window) {
	if (glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS) {
		glfwSetWindowShouldClose(window, true);
	}
}
```
