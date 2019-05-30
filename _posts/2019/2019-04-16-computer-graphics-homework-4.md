---
layout: post
title: 计算机图形学作业（ 四）：实现正方体的正交、透视投影并实现一个camera类控制摄像头
date: 2019-04-16 00:00:00
categories: 
- CG-计算机图形学
tags: 
- 计算机图形学
- ImGUI
- OpenGL
- C++
description: 实现正方体的正交、透视投影并实现一个camera类控制摄像头。
---





# 正交投影  {#ortho}
正交投影的示例图如下所示：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-04/2019-04-16-1.png)

上面的平截头体定义了可见的坐标，它由由宽、高、近(Near)平面和远(Far)平面所指定。任何出现在近平面之前或远平面之后的坐标都会被裁剪掉。这种投影会产生不真实的结果，因为这个投影没有将透视(Perspective)考虑进去。

要在程序中使用正射投影，就用以下代码：
```
projection = glm::ortho(ortho_left, ortho_right, ortho_bottom, ortho_top, ortho_near, ortho_far);
```
前两个参数指定了平截头体的左右坐标，第三和第四参数指定了平截头体的底部和顶部。通过这四个参数我们定义了近平面和远平面的大小，然后第五和第六个参数则定义了近平面和远平面的距离。

最终效果如下图：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-04/2019-04-16-2.png)

# 透视投影  {#persperctive}
透视投影的示例图如下：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-04/2019-04-16-3.png)

由于透视，你会注意到离你越远的东西看起来更小，正更符合现实生活体验。

要使用透视投影，就是用以下代码：
```
projection = glm::perspective(glm::radians(perspective_fov), perspective_division, perspective_near, perspective_far);
```
它的第一个参数定义了fov的值，它表示的是视野(Field of View)，并且设置了观察空间的大小，它的值通常设置为45.0f。第二个参数设置了宽高比，由视口的宽除以高所得。第三和第四个参数设置了平截头体的近和远平面。

最终效果图如下：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-04/2019-04-16-4.png)

# 视角变换  {#view-change}
把cube放置在(0, 0, 0)处，做透视投影，使摄像机围绕cube旋转，并且时刻看着cube中心 。

为了达成上述目标，首先我们要创建一个摄像机，也就是view观察矩阵，创建摄像机的观察矩阵，我们会用到lookAt函数，如下所示：
```
glm::mat4 view;
view = glm::lookAt(glm::vec3(0.0f, 0.0f, 3.0f), 
           glm::vec3(0.0f, 0.0f, 0.0f), 
           glm::vec3(0.0f, 1.0f, 0.0f));
```
该函数的第一个参数是摄像机位置，第二个参数是要观察的目标的位置，第三个参数是摄像机的上向量。

那么，为了使摄像机绕cube旋转并观察，我们需要修改lookAt函数中的第一个参数，也就是摄像机的位置坐标要呈现出一个圆形的变化。我们用以下方法实现：
```
float radius = 10.0f;
float camX = sin(glfwGetTime()) * radius;
float camZ = cos(glfwGetTime()) * radius;
glm::mat4 view;
view = glm::lookAt(glm::vec3(camX, 0.0, camZ), glm::vec3(0.0, 0.0, 0.0), glm::vec3(0.0, 1.0, 0.0));
```
这样的话，摄像机在x0y平面上，就会随时间变化呈现出一个圆形移动。

最终结果如下：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-04/2019-04-16-5.png)

# 对摄像机空间与物体空间的理解  {#conhension}
在现实生活中，我们一般将摄像机摆放的空间View matrix和被拍摄的物体摆设的空间Model matrix分开，但 是在OpenGL中却将两个合二为一设为ModelView matrix，通过上面的作业启发，你认为是为什么呢？

我认为这是OpenGL中固定管线与可编程管线的区别。  
在固定管线中，摄像机摆放的空间View matrix和被拍摄的物体摆设的空间Model matrix是合二为一的，固定渲染管线的OpenGLES不需要也不允许你自己去定义顶点渲染和像素渲染的具体逻辑，它内部已经固化了一套完整的渲染流程，只需要开发者在CPU代码端输入渲染所需要的参数并指定特定的开关，就能完成不同的渲染，有一定的局限性。   
而在可编程管线中，两种矩阵是分开的，可编程渲染管线的OpenGLES版本必须由开发者自行实现渲染流程，否则无法绘制出最终的画面。开发者可以根据自己的具体需要来编写顶点渲染和像素渲染中的具体逻辑，可最大程度的简化渲染管线的逻辑以提高渲染效率，也可自己实现特定的算法和逻辑来渲染出固定管线无法渲染的效果。具有很高的可定制性，但同时也对开发者提出了更高的要求。

# camera类（加分项）  {#camera}
camera类的源码可以在OpenGL的官方教程中找到：[https://learnopengl.com/code_viewer_gh.php?code=includes/learnopengl/camera.h](https://learnopengl.com/code_viewer_gh.php?code=includes/learnopengl/camera.h)，只要在项目中引用此头文件即可，重点在于理解和使用该camera类。

## 移动  {#move}
为了使用该camera类，首先我们要初始化一个camera类，并初始化一些参数，如下所示：
```
Camera camera(glm::vec3(0.0f, 0.0f, 10.0f));
//初始鼠标位置
float mouseX = 0.0f;
float mouseY = 0.0f;
bool firstMouse = true;
//当前帧与上一帧的时间差
float deltaTime = 0.0f;
//上一帧的时刻
float lastFrame = 0.0f;
```
其中，deltaTime，lastFrame这两个变量是用来衡量当前计算的渲染速度， 如果我们的deltaTime很大，就意味着上一帧的渲染花费了更多时间。当实现camera的移动速度时，我们要结合机器渲染速度，即deltaTime值，使得camera的移动速度较为平滑。

然后我们控制键盘输入的在processInput函数中，增添摄像机移动命令，就可实现摄像机的移动,如下：
```
void processInput(GLFWwindow* window) {
	if (glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS) {
		glfwSetWindowShouldClose(window, true);
	}

	if (glfwGetKey(window, GLFW_KEY_W) == GLFW_PRESS)
		camera.ProcessKeyboard(FORWARD, deltaTime);
	if (glfwGetKey(window, GLFW_KEY_S) == GLFW_PRESS)
		camera.ProcessKeyboard(BACKWARD, deltaTime);
	if (glfwGetKey(window, GLFW_KEY_A) == GLFW_PRESS)
		camera.ProcessKeyboard(LEFT, deltaTime);
	if (glfwGetKey(window, GLFW_KEY_D) == GLFW_PRESS)
		camera.ProcessKeyboard(RIGHT, deltaTime);
}
```

## 视角  {#view}

然后便是鼠标对摄像机俯视角和偏航角的控制，我们在main.cpp里先声明定义鼠标事件回调函数。如下：
```
//鼠标移动
void mouse_callback(GLFWwindow* window, double xpos, double ypos) {
	if (firstMouse)
	{
		mouseX = xpos;
		mouseY = ypos;
		firstMouse = false;
	}

	float xoffset = xpos - mouseX;
	float yoffset = mouseY - ypos;

	mouseX = xpos;
	mouseY = ypos;

	camera.ProcessMouseMovement(xoffset, yoffset);
}
```
然后再通知glfw使用这回调函数，如下
```
glfwSetCursorPosCallback(window, mouse_callback);
```
还有，最重要的一点是，增加这条配置语句，告诉glfw捕获我们的鼠标移动，否则视角的左右移动将无法大于90度。
```
glfwSetInputMode(window, GLFW_CURSOR, GLFW_CURSOR_DISABLED);
```

## 放缩  {#scroll}
最后是实现滑轮的放缩功能，首先是生命定义滑轮的回调函数，如下：
```
void scroll_callback(GLFWwindow* window, double xoffset, double yoffset) {
	camera.ProcessMouseScroll(yoffset);
}
```
然后是通知glfw使用滑轮回调函数
```
glfwSetScrollCallback(window, scroll_callback);
```
最后是在cube的投影矩阵，使用camera类的zoom放缩参数
```
projection = glm::perspective(glm::radians(camera.Zoom), perspective_division, perspective_near, perspective_far);
```

## 效果  {#result}
最终结果如下所示：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-04/2019-04-16-6.png)

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
#include "camera.h"

using namespace std;

const int WINDOW_WIDTH = 1000;
const int WINDOW_HEIGHT = 800;

void framebuffer_size_callback(GLFWwindow* window, int width, int height);
void mouse_callback(GLFWwindow* window, double xpos, double ypos);
void scroll_callback(GLFWwindow* window, double xoffset, double yoffset);
void processInput(GLFWwindow* window);

//顶点着色器可以向片段着色器传数据
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

Camera camera(glm::vec3(0.0f, 0.0f, 10.0f));
//鼠标位置
float mouseX = 0.0f;
float mouseY = 0.0f;
bool firstMouse = true;
//每一帧间隔时间
float deltaTime = 0.0f;
//上一帧的时刻
float lastFrame = 0.0f;

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
	glfwSetCursorPosCallback(window, mouse_callback);
	glfwSetScrollCallback(window, scroll_callback);

	if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress)) {
		std::cout << "Failed to initialize GLAD" << std::endl;
		return -1;
	}

	//深度测试
	glEnable(GL_DEPTH_TEST);

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

	float scaleDelta = 1.0f;
	float rotateAngle = 0.5f;
	float ortho_left = -10.f; 
	float ortho_right = 10.0f;
	float ortho_bottom = -10.0f;
	float ortho_top = 10.0f;
	float ortho_near = 0.1f;
	float ortho_far = 100.0f;
	float perspective_fov = 45.0f;
	float perspective_division = (float)WINDOW_WIDTH / (float)WINDOW_HEIGHT;
	float perspective_near = 0.1f;
	float perspective_far = 100.0f;
	//0代表正交投影，1代表透视投影，2代表使摄像机围绕cube旋转
	bool is_ortho = true;
	bool is_perspective = false;
	bool is_view_change = false;
	bool enable_camera = false;
	

	while (!glfwWindowShouldClose(window)) {
		if (enable_camera) {
			//告诉glfw获取我们的鼠标
			glfwSetInputMode(window, GLFW_CURSOR, GLFW_CURSOR_DISABLED);
		}
		else {
			glfwSetInputMode(window, GLFW_CURSOR, GLFW_CURSOR_NORMAL);
		}
	

		//计算当前时间和帧间隔时间
		float currentFrame = glfwGetTime();
		deltaTime = currentFrame - lastFrame;
		lastFrame = currentFrame;

		processInput(window);

		//创建坐标变换
		glm::mat4 model = glm::mat4(1.0f);
		glm::mat4 view = glm::mat4(1.0f);
		glm::mat4 projection = glm::mat4(1.0f);

		if (is_view_change) {
			float radius = 20.0f;
			float camX = sin(glfwGetTime()) * radius;
			float camZ = cos(glfwGetTime()) * radius;
			view = glm::lookAt(glm::vec3(camX, 0.0, camZ), glm::vec3(0.0, 0.0, 0.0), glm::vec3(0.0, 1.0, 0.0));
		}
		else {
			if (enable_camera) {
				view = camera.GetViewMatrix();
			}
			else {
				view = glm::translate(view, glm::vec3(0.0f, 0.0f, -10.0f));
			}
		}

		if (is_ortho) {
			//两个参数指定了平截头体的左右坐标，第三和第四参数指定了平截头体的底部和顶部，然后第五和第六个参数则定义了近平面和远平面的距离
			projection = glm::ortho(ortho_left, ortho_right, ortho_bottom, ortho_top, ortho_near, ortho_far);
		}
		else if (is_perspective) {
			//它的第一个参数定义了fov的值，它表示的是视野，并且设置了观察空间的大小。第二个参数设置了宽高比。第三和第四个参数设置了平截头体的近和远平面
			//glm::radians()是把角度转为弧度
			if (enable_camera) {
				//利用鼠标放缩
				projection = glm::perspective(glm::radians(camera.Zoom), perspective_division, perspective_near, perspective_far);
			}
			else {
				projection = glm::perspective(glm::radians(perspective_fov), perspective_division, perspective_near, perspective_far);
			}
			
		}
		else if (is_view_change) {
			projection = glm::perspective(glm::radians(perspective_fov), perspective_division, perspective_near, perspective_far);
		}

		//使用着色器程序
		glUseProgram(shaderProgram);

		//获取着色器程序uniform
		unsigned int modelLoc = glGetUniformLocation(shaderProgram, "model");
		unsigned int viewLoc = glGetUniformLocation(shaderProgram, "view");
		unsigned int projectionLoc = glGetUniformLocation(shaderProgram, "projection");
		glUniformMatrix4fv(viewLoc, 1, GL_FALSE, &view[0][0]);
		glUniformMatrix4fv(projectionLoc, 1, GL_FALSE, &projection[0][0]);
		
		glfwPollEvents();

		//清除屏幕
		glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
		glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

		glBindVertexArray(VAO);
		//model包含了位移、缩放与旋转操作
		if (is_view_change) {
			model = glm::mat4(1.0f);
			model = glm::scale(model, glm::vec3(scaleDelta, scaleDelta, scaleDelta));
			model = glm::rotate(model, rotateAngle, glm::vec3(1.0f, 0.0f, 0.0f));
			//画两个三角形，方便判断是摄像机旋转，而不是cube旋转，第二个参数代表三角形的个数 * 3
			for (int i = 0; i < 2; i++) {
				if (i == 0) {
					model = glm::translate(model, glm::vec3(0, 0, 0));
				}
				else {
					model = glm::translate(model, glm::vec3(-5.5, -3.0, 0.0));
				}
				glUniformMatrix4fv(modelLoc, 1, GL_FALSE, glm::value_ptr(model));
				glDrawElements(GL_TRIANGLES, 36, GL_UNSIGNED_INT, 0);
			}
		}
		else {
			model = glm::mat4(1.0f);
			model = glm::scale(model, glm::vec3(scaleDelta, scaleDelta, scaleDelta));
			model = glm::translate(model, glm::vec3(-1.5, 0.5, -1.5));
			model = glm::rotate(model, rotateAngle, glm::vec3(1.0f, 0.0f, 0.0f));
			glUniformMatrix4fv(modelLoc, 1, GL_FALSE, glm::value_ptr(model));
			glDrawElements(GL_TRIANGLES, 36, GL_UNSIGNED_INT, 0);
		}

		//创建imgui
		ImGui_ImplOpenGL3_NewFrame();
		ImGui_ImplGlfw_NewFrame();
		ImGui::NewFrame();

		ImGui::Begin("Edit cube", &show_window, ImGuiWindowFlags_MenuBar);
		if (is_perspective) {
			if (ImGui::Button("enable camera")) {
				if (enable_camera) {
					enable_camera = false;
				}
				else {
					enable_camera = true;
				}
			}
		}

		if (ImGui::RadioButton("ortho", is_ortho)) {
			is_ortho = true;
			is_perspective = false;
			is_view_change = false;
		}
		if (ImGui::RadioButton("perspective", is_perspective)) {
			is_ortho = false;
			is_perspective = true;
			is_view_change = false;
		}
		if (ImGui::RadioButton("View Changing", is_view_change)) {
			is_ortho = false;
			is_perspective = false;
			is_view_change = true;
		}

		if (is_ortho) {
			ImGui::SliderFloat("left", &ortho_left, -50.0f, 50.0f);
			ImGui::SliderFloat("right", &ortho_right, -50.0f, 50.0f);
			ImGui::SliderFloat("bottom", &ortho_bottom, -50.0f, 50.0f);
			ImGui::SliderFloat("top", &ortho_top, -50.0f, 50.0f);
			ImGui::SliderFloat("near", &ortho_near, 0.1f, 50.0f);
			ImGui::SliderFloat("far", &ortho_far, 0.1f, 50.0f);
		}
		else if (is_perspective) {
			ImGui::SliderFloat("fov", &perspective_fov, 0.0f, 100.0f);
			ImGui::SliderFloat("width/height", &perspective_division, 0.0f, 5.0f);
			ImGui::SliderFloat("near", &perspective_near, 0.1f, 50.0f);
			ImGui::SliderFloat("far", &perspective_far, 0.1f, 50.0f);
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

	if (glfwGetKey(window, GLFW_KEY_W) == GLFW_PRESS)
		camera.ProcessKeyboard(FORWARD, deltaTime);
	if (glfwGetKey(window, GLFW_KEY_S) == GLFW_PRESS)
		camera.ProcessKeyboard(BACKWARD, deltaTime);
	if (glfwGetKey(window, GLFW_KEY_A) == GLFW_PRESS)
		camera.ProcessKeyboard(LEFT, deltaTime);
	if (glfwGetKey(window, GLFW_KEY_D) == GLFW_PRESS)
		camera.ProcessKeyboard(RIGHT, deltaTime);
}

//鼠标移动
void mouse_callback(GLFWwindow* window, double xpos, double ypos) {
	if (firstMouse)
	{
		mouseX = xpos;
		mouseY = ypos;
		firstMouse = false;
	}

	float xoffset = xpos - mouseX;
	float yoffset = mouseY - ypos;

	mouseX = xpos;
	mouseY = ypos;

	camera.ProcessMouseMovement(xoffset, yoffset);
}

//鼠标放缩
void scroll_callback(GLFWwindow* window, double xoffset, double yoffset) {
	camera.ProcessMouseScroll(yoffset);
}
```

