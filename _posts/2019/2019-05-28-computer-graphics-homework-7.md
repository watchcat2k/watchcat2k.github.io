---
layout: post
title: 计算机图形学作业（ 七）：利用 OpenGL 绘制 Bezier 贝塞尔曲线
date: 2019-05-28 00:00:00
categories: 
- CG-计算机图形学
tags: 
- 计算机图形学
- ImGUI
- OpenGL
- C++
- Bezier
description: 利用 OpenGL 绘制 Bezier 贝塞尔曲线。
---



# Bezier 曲线原理
Bezier 曲线的原理我参考了这篇博客：[https://www.cnblogs.com/hyb1/p/3875468.html](https://www.cnblogs.com/hyb1/p/3875468.html)。

Bezier 曲线是应用于二维图形的曲线。曲线由顶点和控制点组成，通过改变控制点坐标可以改变曲线的形状。

**一次 Bezier 曲线公式：**

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-05/2019-05-28-1.gif)

一次 Bezier 曲线是由 P0 至 P1 的连续点，描述的一条线段。

**二次 Bezier 曲线公式：**

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-05/2019-05-28-2.gif)

二次 Bezier 曲线是 P0 至 P1 的连续点 Q0 和 P1 至 P2 的连续点 Q1 组成的线段上的连续点 B(t)，描述一条抛物线。

**三次 Bezier 曲线公式：**

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-05/2019-05-28-3.gif)

由此可得 Bezier 曲线的一般方程为：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-05/2019-05-28-4.png)

# OpenGL 实现思路
在 OpenGL 窗口中，我们希望能通过左键点击窗口添加 Bezier 曲线的控制点，右键点击则对当前添加的最后一个控制点进行消除。然后根据鼠标绘制的控制点实时更新 Bezier 曲线。
## 捕获鼠标点击时的坐标
我们需要用一个回调函数，该函数是在鼠标移动时不断获取鼠标在窗口的坐标。

首先我们要声明全局的鼠标位置变量，代码如下：
```
float mouseXPos, mouseYPos;
```
然后在鼠标事件中不断更新全局位置变量的值。代码如下
```
void cursor_position_callback(GLFWwindow* window, double x, double y) { 
	mouseXPos = float((x - WINDOW_WIDTH / 2) / WINDOW_WIDTH) * 2;
	mouseYPos = float(0 - (y - WINDOW_HEIGHT / 2) / WINDOW_HEIGHT) * 2;
	return; 
}
```

## 根据顶点画出连续的线段
前面我们获取的鼠标的当前位置，那么当鼠标点击左键时，我们要捕获该点击事件，将顶点数据添加到 lineVertices 并通过绑定 VAO 画出线段。

先声明全局的顶点数据变量：
```
// 声明全局顶点变量, 点个数为 vertexLen / 3
int lineVertexLen = 0;
float lineVertices[MAX_VERTEX_LEN] = {
	//位置
	//-0.5f, 0.5f, 0.0f
};
```
然后再鼠标点击事件中操作：
```
void mouse_button_callback(GLFWwindow* window, int button, int action, int mods) { 
	if (action == GLFW_PRESS) switch (button) { 
		case GLFW_MOUSE_BUTTON_LEFT:			
			// 每隔两个点画一条直线
			// 鼠标点击的点
			lineVertices[lineVertexLen] = mouseXPos;
			lineVertexLen++;
			lineVertices[lineVertexLen] = mouseYPos;
			lineVertexLen++;
			lineVertices[lineVertexLen] = 0.0f;
			lineVertexLen++;
			// 添加索引,前一个点也新的点一起确定新线段
			if (lineIndicesLen >= 2) {
				lineIndices[lineIndicesLen] = lineIndices[lineIndicesLen - 1];
				lineIndicesLen++;
				lineIndices[lineIndicesLen] = lineIndices[lineIndicesLen - 1] + 1;
				lineIndicesLen++;
			}
			else {
				lineIndices[lineIndicesLen] = lineIndicesLen;
				lineIndicesLen++;
			}
			break;
		default:				
			break;
	}	
	return; 
}
```
之后便是通过 VAO、VBO 和 GL_LINES 等画出线段。

## 根据顶点画出 Bezier 贝塞尔曲线
根据之前的 Bezier 曲线一般式，我们能很容易地根据顶点计算出 Bezier 曲线的点数据。只要新声明一个函数，传入顶点的数据和长度，就能计算出各个位置上的 Bezier 曲线的点数据，如下：
```
int getBezierVertex(float lineVertices[MAX_VERTEX_LEN], int lineVertexLen, float bezierVertices[MAX_BEZIER_VERTEX_LEN]) {
	int bezierVertexLen = 0;
	if (lineVertexLen == 0) return bezierVertexLen;
	else if (lineVertexLen == 3) {
		bezierVertices[bezierVertexLen] = lineVertices[0];
		bezierVertexLen++;
		bezierVertices[bezierVertexLen] = lineVertices[1];
		bezierVertexLen++;
		bezierVertices[bezierVertexLen] = lineVertices[2];
		bezierVertexLen++;
	}
	else {
		for (float t = 0.000f; t <= 1.000f; t = t + 0.001f) {
			double new_xPos = 0, new_yPos = 0;
			for (int index = 0; index < lineVertexLen / 3; index++) {
				// x坐标
				new_xPos += lineVertices[index * 3] * pow(t, index) * pow((1 - t), (lineVertexLen / 3 - 1 - index));
				// y坐标
				new_yPos += lineVertices[index * 3 + 1] * pow(t, index) * pow((1 - t), (lineVertexLen / 3 - 1 - index));
			}
			bezierVertices[bezierVertexLen] = new_xPos;
			bezierVertexLen++;
			bezierVertices[bezierVertexLen] = new_yPos;
			bezierVertexLen++;
			bezierVertices[bezierVertexLen] = 0.0f;
			bezierVertexLen++;
		}
	}
	return bezierVertexLen;
}
```
之后便可以通过 VAO、VBO 和 GL_POINTS 等画出 Bezier 曲线。

# 效果

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-05/2019-05-28-5.png)

# 代码
```
#include <iostream>
#include <cmath>
#include <glad/glad.h>
#include <GLFW/glfw3.h>

#include "imgui.h"
#include "imgui_impl_glfw.h"
#include "imgui_impl_opengl3.h"

#include <glm.hpp>
#include <gtc/matrix_transform.hpp>
#include <gtc/type_ptr.hpp>

// 加载纹理的库
#include "stb_image.h"

//使用了官方的shader库文件，可以更方便地操作shader
//注意，使用官方库操作shadder时，自己写的顶点和片段着色器代码必须另外写成文件
//然后把文件路径传入shader.h提供的构造函数
#include "shader.h"  

using namespace std;

const int WINDOW_WIDTH = 1000;
const int WINDOW_HEIGHT = 800;
const int MAX_VERTEX_LEN = 300;
const int MAX_INDICES_LEN = 600;
const int MAX_BEZIER_VERTEX_LEN = 3000;

void framebuffer_size_callback(GLFWwindow* window, int width, int height);
void processInput(GLFWwindow* window);
void mouse_button_callback(GLFWwindow* window, int button, int action, int mods);
void cursor_position_callback(GLFWwindow* window, double x, double y);
int getBezierVertex(float lineVertices[MAX_VERTEX_LEN],int lineVertexLen, float bezierVertices[MAX_BEZIER_VERTEX_LEN]);

// 声明鼠标全局位置变量
float mouseXPos, mouseYPos;

// 声明全局顶点变量, 点个数为 vertexLen / 3
int lineVertexLen = 0;
float lineVertices[MAX_VERTEX_LEN] = {
	//位置
	//-0.5f, 0.5f, 0.0f,
	//0.5f, 0.5f, 0.0f,
	//0.5f, -0.5f, 0.0f
};
int lineIndicesLen = 0;
unsigned int lineIndices[MAX_INDICES_LEN] = { // 注意索引从0开始! 
	//0, 1, // 第一个线段
	//1, 2
};


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
	glfwSetMouseButtonCallback(window, mouse_button_callback);
	glfwSetCursorPosCallback(window, cursor_position_callback);

	if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress)) {
		std::cout << "Failed to initialize GLAD" << std::endl;
		return -1;
	}

	// 深度测试
	glEnable(GL_DEPTH_TEST);

	//创建并绑定ImGui
	const char* glsl_version = "#version 130";
	IMGUI_CHECKVERSION();
	ImGui::CreateContext();
	ImGuiIO& io = ImGui::GetIO(); (void)io;
	ImGui::StyleColorsDark();
	ImGui_ImplGlfw_InitForOpenGL(window, true);
	ImGui_ImplOpenGL3_Init(glsl_version);

	Shader lineShader("vertexShader.vs", "lineFragmentShader.fs");
	Shader bezierShader("vertexShader.vs", "bezierFragmentShader.fs");

	bool show_window = true;

	while (!glfwWindowShouldClose(window)) {
		processInput(window);

		//清除屏幕
		glClearColor(0.1f, 0.1f, 0.1f, 1.0f);
		glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

		unsigned int VAO;
		unsigned int VBO;
		unsigned int EBO;

		//必须先绑定VA0
		glGenVertexArrays(1, &VAO);
		glBindVertexArray(VAO);

		//再绑定VBO
		glGenBuffers(1, &VBO);
		glBindBuffer(GL_ARRAY_BUFFER, VBO);
		glBufferData(GL_ARRAY_BUFFER, sizeof(lineVertices), lineVertices, GL_STATIC_DRAW);

		//使用EBO画多个线段，组合成其它图形
		glGenBuffers(1, &EBO);
		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
		glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(lineIndices), lineIndices, GL_STATIC_DRAW);

		//再设置属性
		//位置属性
		//属性位置值为0的顶点属性
		glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
		glEnableVertexAttribArray(0);

		glBindBuffer(GL_ARRAY_BUFFER, 0);

		glEnableVertexAttribArray(1);

		// 使用着色器程序
		lineShader.use();

		//画线段，
		glBindVertexArray(VAO);
		// 因为通过 indices 确定线段，每两点确定一线段，所以画多少线段，就填入线段数乘以2
		if (lineIndicesLen >= 2) {
			glDrawElements(GL_LINES, lineIndicesLen, GL_UNSIGNED_INT, 0);
		}


		// 以下是 bezier 点数据
		// 声明 bezier 顶点变量 
		int bezierVertexLen = 0;
		float bezierVertices[MAX_BEZIER_VERTEX_LEN] = {
			//-0.5f, 0.5f, 0.0f,
		};

		bezierVertexLen = getBezierVertex(lineVertices, lineVertexLen, bezierVertices);

		unsigned int bezierVAO;
		unsigned int bezierVBO;

		//必须先绑定VA0
		glGenVertexArrays(1, &bezierVAO);
		glBindVertexArray(bezierVAO);

		//再绑定VBO
		glGenBuffers(1, &bezierVBO);
		glBindBuffer(GL_ARRAY_BUFFER, bezierVBO);
		glBufferData(GL_ARRAY_BUFFER, sizeof(bezierVertices), bezierVertices, GL_STATIC_DRAW);

		//再设置属性
		//位置属性
		//属性位置值为0的顶点属性
		glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
		glEnableVertexAttribArray(0);

		glBindBuffer(GL_ARRAY_BUFFER, 0);

		glEnableVertexAttribArray(1);

		// 使用着色器程序
		bezierShader.use();

		//画线段，
		glBindVertexArray(bezierVAO);
		glDrawArrays(GL_POINTS, 0, bezierVertexLen / 3);

		//创建imgui
		ImGui_ImplOpenGL3_NewFrame();
		ImGui_ImplGlfw_NewFrame();
		ImGui::NewFrame();

		ImGui::Begin("Edit cube", &show_window, ImGuiWindowFlags_MenuBar);


		ImGui::End();

		ImGui::Render();
		int display_w, display_h;
		glfwMakeContextCurrent(window);
		glfwGetFramebufferSize(window, &display_w, &display_h);
		glViewport(0, 0, display_w, display_h);
		ImGui_ImplOpenGL3_RenderDrawData(ImGui::GetDrawData());

		glfwPollEvents();
		glfwSwapBuffers(window);
	}

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

void mouse_button_callback(GLFWwindow* window, int button, int action, int mods) { 
	if (action == GLFW_PRESS) switch (button) { 
		case GLFW_MOUSE_BUTTON_LEFT:			
			// 每隔两个点画一条直线
			// 鼠标点击的点
			lineVertices[lineVertexLen] = mouseXPos;
			lineVertexLen++;
			lineVertices[lineVertexLen] = mouseYPos;
			lineVertexLen++;
			lineVertices[lineVertexLen] = 0.0f;
			lineVertexLen++;
			// 添加索引,前一个点也新的点一起确定新线段
			if (lineIndicesLen >= 2) {
				lineIndices[lineIndicesLen] = lineIndices[lineIndicesLen - 1];
				lineIndicesLen++;
				lineIndices[lineIndicesLen] = lineIndices[lineIndicesLen - 1] + 1;
				lineIndicesLen++;
			}
			else {
				lineIndices[lineIndicesLen] = lineIndicesLen;
				lineIndicesLen++;
			}
			break;
		case GLFW_MOUSE_BUTTON_RIGHT:
			if (lineVertexLen >= 3) {
				lineVertexLen = lineVertexLen - 3;
			}

			if (lineIndicesLen >= 4) {
				lineIndicesLen = lineIndicesLen - 2;
			}
			else {
				lineIndicesLen = lineIndicesLen - 1;
			}

			break;
		default:				
			break;
	}	
	return; 
}

void cursor_position_callback(GLFWwindow* window, double x, double y) { 
	mouseXPos = float((x - WINDOW_WIDTH / 2) / WINDOW_WIDTH) * 2;
	mouseYPos = float(0 - (y - WINDOW_HEIGHT / 2) / WINDOW_HEIGHT) * 2;
	return; 
}

int getBezierVertex(float lineVertices[MAX_VERTEX_LEN], int lineVertexLen, float bezierVertices[MAX_BEZIER_VERTEX_LEN]) {
	int bezierVertexLen = 0;
	if (lineVertexLen == 0) return bezierVertexLen;
	else if (lineVertexLen == 3) {
		bezierVertices[bezierVertexLen] = lineVertices[0];
		bezierVertexLen++;
		bezierVertices[bezierVertexLen] = lineVertices[1];
		bezierVertexLen++;
		bezierVertices[bezierVertexLen] = lineVertices[2];
		bezierVertexLen++;
	}
	else {
		for (float t = 0.000f; t <= 1.000f; t = t + 0.001f) {
			double new_xPos = 0, new_yPos = 0;
			for (int index = 0; index < lineVertexLen / 3; index++) {
				// x坐标
				new_xPos += lineVertices[index * 3] * pow(t, index) * pow((1 - t), (lineVertexLen / 3 - 1 - index));
				// y坐标
				new_yPos += lineVertices[index * 3 + 1] * pow(t, index) * pow((1 - t), (lineVertexLen / 3 - 1 - index));
			}
			bezierVertices[bezierVertexLen] = new_xPos;
			bezierVertexLen++;
			bezierVertices[bezierVertexLen] = new_yPos;
			bezierVertexLen++;
			bezierVertices[bezierVertexLen] = 0.0f;
			bezierVertexLen++;
		}
	}
	return bezierVertexLen;
}

```
