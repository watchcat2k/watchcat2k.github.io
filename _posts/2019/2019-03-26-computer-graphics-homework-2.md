---
layout: post
title: 计算机图形学作业（ 二）：使用Bresenham算法画直线和圆，并使用光栅化算法填充三角形
date: 2019-03-26 00:00:00
categories: 
- CG-计算机图形学
tags: 
- 计算机图形学
- ImGUI
- OpenGL
- C++
- Bresenham
description: 使用Bresenham算法画直线和圆，并使用光栅化算法填充三角形。
---





# Bresenham算法画直线  {#line}
## 原理  {#line-theory}
首先，观察下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-26-1.png)

设一条直线为y=mx+B，那么上图图中的参数为：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-26-8.png)

然后观察下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-26-2.png)

在图中，红色点为当前的点，我们要计算出下一个点是取高位的黄色点，还是低位的黄色点，就要比较这两个点谁距离直线最近，结合之前的图，可得：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-26-7.png)

如果d_{lower}-d_{upper}>0，则取上方的黄色点，否则取下方的黄色点。
设定一个参数p_i，如下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-26-3.png)

经过计算可得：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-26-9.png)

且有以下关系：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-26-4.png)

## 算法  {#line-algorithm}
绘制斜率在[0,1]范围内的直线算法如下：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-26-5.png)

## 拓展  {#line-extension}
由于使用Bresenham算法只能绘制斜率在[0,1]范围内的直线，那么要绘制其它斜率范围的直线，就要进行坐标平移、对称变换。

首先，我在代码中输入三个正整数点坐标，由于在OpenGL中坐标范围是 -1.0f~1.0f，所以我定义了一个函数`normalize`，负责把正整数转化为 -1.0f~1.0f 的浮点数。因为我的窗口大小是800*800，所以只需要写成以下形式：
```
float normalize(int input) {
	float result = float(input) / 800;
	return result;
}
```

然后，对于给出的两个点，我们要绘制这两点的直线，首先计算出它们的斜率，若斜率不存在，则固定横坐标，循环纵坐标画出一个个点；若斜率在[0,1]范围，则以Bresenham算法绘制；否则，按以下方法绘制直线：
1. 找出这两点谁的横坐标较小，然后把横坐标较小的点平移到原点，横坐标较大的点平移到对应位置。
2. 对横坐标较大的点做一系列变换（关于直线y=x或y=0等对称），使得两点斜率在[0,1]之内
3. 使用Bresenham算法绘制出直线，然后把这条直线作步骤2相反的一系列变换，再加上步骤1的平移量即可。

## 结果  {#line-result}
 画出三角形并光栅化后，结果如下图所示：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-26-10.png)
 
# 使用光栅化算法填充三角形  {#rasterization}
光栅化算法有三种：
1. Edge-walking
2. Edge-equation
3. Barycentric-coordinate based

我使用的算法是Edge-equation
 
## 算法伪代码  {#rasterization-algorithm}
 
![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-26-12.png)
 
## 算法解释  {#rasterization-explaination}
 1. 根据三角形的三个点，计算三条边的一般式方程Ax+By+C=0，其中A=Y2-Y1，B=X1-X2，C=X2*Y1-X1*Y2。
 2. 将三条边“中心化”，取三角形的中点，即((X1+X2+X3)/3, (Y1+Y2+Y3)/3)，分别代入三个直线方程，若最终结果小于0，则把该方程的三个参数A、B、C分别乘以-1。
 3. 计算三角形外接矩阵，该矩阵的两条边分别与X轴Y轴平行。即在三角形的三个顶点中，找到最小的x值leftX和最大的x值rightX，那么外接矩阵的横边就从leftX开始到rightX结束，同理竖边从bottomY开始到topY结束。
 4. 遍历外接矩阵的每一个点，每个点都代入三个直线方程，若结果都大于0，则该点在三角形内，画出该点。

## 结果  {#rasterization-result}
 ![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-26-10.png)
 
# Bresenham算法画圆  {#circle}
## 原理  {#circle-theory}
我是用八分法画圆，因为圆上的一点，可以有另外在圆上的7个对称点，我们只需要画八分之一的圆。如下图：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-26-6.png)

我们只需要画第一象限的上半部分的八分之一圆。画图时，计算出的点作对称变换就可得到其余7个对称点，最后就能获得一整个圆的所有点。用GL_POINTS即可画出圆。

具体推导方法我是参考了这篇博客[https://blog.csdn.net/mayh554024289/article/details/44781531](https://blog.csdn.net/mayh554024289/article/details/44781531)，故不再赘述。

## 算法  {#circle-algorithm}
我们假设圆心在原点（若不在原点，只需要作简单平移变换即可），并且主要画第一象限的上半部分的八分之一圆，其余部分用对称方法解决。
1. 取初始值x=0, y=radius, endX=radius/(float)sqrt(2), d=1.25-radius
2. 如果d<=0，令(x+1, y)取代(x,y)，并且d=d+2*x+2；否则令(x+1, y-1)取代(x,y)，并且d=d+2*(x-y)+5。
3. 画出当前(x,y)点和其余7个对称点。
4. 当x<=endX，重复步骤2~3。

## 结果  {#circle-result}
画出的圆如下所示：

![在这里插入图片描述](https://raw.githubusercontent.com/watchcat2k/watchcat2k.github.io/master/styles/images/blogImage/2019-03/2019-03-26-11.png)
 
# 代码  {#code}
```
#include <iostream>
#include <algorithm>
#include <glad/glad.h>
#include <GLFW/glfw3.h>
#include "imgui.h"
#include "imgui_impl_glfw.h"
#include "imgui_impl_opengl3.h"

CONST INT MAX_LENGTH = 240000;
CONST INT WINDOW_SIZE = 800;

void framebuffer_size_callback(GLFWwindow* window, int width, int height);
void processInput(GLFWwindow* window);
void getLineVertex(int x1, int y1, int x2, int y2, float vertices[MAX_LENGTH], int* length);
void edge_equations(int point1[2], int point2[2], int point3[2], float vertices[MAX_LENGTH], int* length);
void getCircleVertex(int originPointX, int originPointY, int radius, float vertices[MAX_LENGTH], int* length);
float normalize(int input);
void initImGUI(GLFWwindow* window);
void createAndRenderLineImGUI(GLFWwindow* window, bool *show_window, int point1[2], int point2[2], int point3[2], bool *isDrawLine);
void createAndRenderCircleImGUI(GLFWwindow* window, bool *show_window, int point1[2], int *radius, bool *isDrawLine);
int myMin(int a, int b, int c);
int myMax(int a, int b, int c);

//顶点着色器可以向片段着色器传数据
const char* vertexShaderSource = "#version 330 core\n"
"layout (location = 0) in vec3 aPos;\n"
"void main()\n"
"{\n"
"gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);\n"
"}\0";

const char* fragmentShaderSource = "#version 330 core\n"
"out vec4 FragColor;\n"
"void main()\n"
"{\n"
"FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f);\n"
"}\0";

int main() {
	//初始化opengl窗口和配置
	glfwInit();
	glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
	glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
	glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);

	GLFWwindow* window = glfwCreateWindow(WINDOW_SIZE, WINDOW_SIZE, "LearnOpenGL", NULL, NULL);
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

	initImGUI(window);

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
	bool isDrawLine = true;

	int point1[2] = { -150, -150 };
	int point2[2] = { 150, -150 };
	int point3[2] = { 0, 150 };
	int radius = 100;
	int originPoint[2] = { 0, 0 };

	while (!glfwWindowShouldClose(window)) {
		float vertices[MAX_LENGTH] = { 0 }; // 注意索引从0开始! 
		int length = 0;

		//获取线段点数据
		if (isDrawLine) {
			getLineVertex(point1[0], point1[1], point2[0], point2[1], vertices, &length);
			getLineVertex(point2[0], point2[1], point3[0], point3[1], vertices, &length);
			getLineVertex(point1[0], point1[1], point3[0], point3[1], vertices, &length);
			edge_equations(point1, point2, point3, vertices, &length);
		}
		else {
			//获取圆数据
			getCircleVertex(originPoint[0], originPoint[1], radius, vertices, &length);
		}

		unsigned int VAO;
		unsigned int VBO;

		//必须先绑定VA0
		glGenVertexArrays(1, &VAO);
		glBindVertexArray(VAO);

		//再绑定VBO
		glGenBuffers(1, &VBO);
		glBindBuffer(GL_ARRAY_BUFFER, VBO);
		glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);

		//再设置属性
		//位置属性
		//属性位置值为0的顶点属性
		glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)0);
		glEnableVertexAttribArray(0);

		glBindBuffer(GL_ARRAY_BUFFER, 0);


		processInput(window);

		//使用着色器程序
		glUseProgram(shaderProgram);

		glfwPollEvents();

		//清除屏幕
		glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
		glClear(GL_COLOR_BUFFER_BIT);

		//画imGUI
		if (isDrawLine) {
			createAndRenderLineImGUI(window, &show_window, point1, point2, point3, &isDrawLine);
		}
		else {
			createAndRenderCircleImGUI(window, &show_window, originPoint, &radius, &isDrawLine);
		}


		//画线段
		//length是vertices的长度，而vertices三个元素为一个点
		//glBindVertexArray(VAO);
		glDrawArrays(GL_POINTS, 0, length / 3);

		glfwSwapBuffers(window);

		glDeleteVertexArrays(1, &VAO);
		glDeleteBuffers(1, &VBO);
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

void getLineVertex(int x1, int y1, int x2, int y2, float vertices[MAX_LENGTH], int* length) {
	int x, y, endx, endy;
	float slope;	//斜率
	bool isSlopeExist = true;
	//确定起初点
	if (x1 < x2) {
		x = x1;
		y = y1;
		endx = x2;
		endy = y2;
	}
	else {
		x = x2;
		y = y2;
		endx = x1;
		endy = y1;
	}

	//计算斜率
	if (x == endx) {
		//斜率不存在
		isSlopeExist = false;
	}
	else {
		slope = ((float)endy - (float)y) / ((float)endx - (float)x);
	}

	//初始点
	vertices[*length] = normalize(x);
	(*length)++;
	vertices[*length] = normalize(y);
	(*length)++;
	vertices[*length] = 0.0f;
	(*length)++;

	if (isSlopeExist == false) {
		//斜率不存在时
		int first;
		int last;
		if (y < endy) {
			first = y;
			last = endy;
		}
		else {
			first = endy;
			last = y;
		}
		for (int i = first; i <= last; i++) {
			vertices[*length] = normalize(x);
			(*length)++;
			vertices[*length] = normalize(i);
			(*length)++;
			vertices[*length] = 0.0f;
			(*length)++;
		}
	}
	else {
		//对称变换前，先把起始点移到原点，其它点移到对应位置
		int moveX = x;
		int moveY = y;
		x = x - moveX;
		y = y - moveY;
		endx = endx - moveX;
		endy = endy - moveY;

		//对称变换
		//利用对称变换,将画其他斜率线段转化为画斜率 0 <=  <= 1的线段
		if (slope > 1) {
			//第一象限上半部分
			//起点不动，互换终点的x y
			int temp = endy;
			endy = endx;
			endx = temp;
		}
		else if (slope < 0 && slope >= -1) {
			//第二象限上半部分
			//起点不动，终点的y变相反数
			endy = -endy;
		}
		else if (slope < -1) {
			//第二象限下半部分
			//起点不动，终点的y变相反数，然后互换终点的x y
			endy = -endy;
			int temp = endy;
			endy = endx;
			endx = temp;
		}

		int deltaX = abs(endx - x);
		int deltaY = abs(endy - y);
		int p = 2 * deltaY - deltaX;

		//只能画斜率 0 <=  <= 1的线段 
		while (x <= endx) {
			if (p <= 0) {
				x = x + 1;
				y = y;
				p = p + 2 * deltaY;
			}
			else {
				x = x + 1;
				y = y + 1;
				p = p + 2 * deltaY - 2 * deltaX;
			}

			//利用对称变换,将斜率 0 <=  <= 1的线段上的点转化为原来斜率线段上的点
			if (slope > 1) {
				//第一象限上半部分
				int temp = y;
				y = x;
				x = temp;
			}
			else if (slope < 0 && slope >= -1) {
				//第二象限上半部分
				//起点不动，终点的y变相反数
				y = -y;
			}
			else if (slope < -1) {
				//第二象限下半部分
				//起点不动，互换x y, y变相反数
				int temp = y;
				y = x;
				x = temp;

				y = -y;
			}

			//对称变换后，先把起始点移到原本位置，然后画出来
			x = x + moveX;
			y = y + moveY;

			vertices[*length] = normalize(x);
			(*length)++;
			vertices[*length] = normalize(y);
			(*length)++;
			vertices[*length] = 0.0f;
			(*length)++;

			//画图后,再恢复起始点
			x = x - moveX;
			y = y - moveY;

			//利用对称变换,恢复原来的点到斜率 0 <=  <= 1的线段上，继续计算下一个点
			if (slope > 1) {
				//第一象限上半部分
				int temp = y;
				y = x;
				x = temp;
			}
			else if (slope < 0 && slope >= -1) {
				//第二象限上半部分
				//起点不动，终点的y变相反数
				y = -y;
			}
			else if (slope < -1) {
				//第二象限下半部分
				//起点不动，y变相反数,互换x y
				y = -y;

				int temp = y;
				y = x;
				x = temp;
			}

		}
	}

}

void edge_equations(int point1[2], int point2[2], int point3[2], float vertices[MAX_LENGTH], int* length) {
	//求3条直线一般方程Ax+By+C=0的参数
	//序号12为point1和point2的参数，序号23为point2和point3的参数，序号13为point1和point3的参数
	int A12, B12, C12;
	int A23, B23, C23;
	int A13, B13, C13;
	A12 = point2[1] - point1[1];
	B12 = point1[0] - point2[0];
	C12 = point2[0] * point1[1] - point1[0] * point2[1];
	A23 = point3[1] - point2[1];
	B23 = point2[0] - point3[0];
	C23 = point3[0] * point2[1] - point2[0] * point3[1];
	A13 = point3[1] - point1[1];
	B13 = point1[0] - point3[0];
	C13 = point3[0] * point1[1] - point1[0] * point3[1];

	//将三角形中点带入各直线，若结果小于0，则将参数A、B、C都乘以-1。 
	float middleX = ((float)point1[0] + (float)point2[0] + (float)point3[0]) / 3;
	float middleY = ((float)point1[1] + (float)point2[1] + (float)point3[1]) / 3;
	float temp = A12 * middleX + B12 * middleY + C12;
	if (temp < 0) {
		A12 = -A12;
		B12 = -B12;
		C12 = -C12;
	}
	temp = A23 * middleX + B23 * middleY + C23;
	if (temp < 0) {
		A23 = -A23;
		B23 = -B23;
		C23 = -C23;
	}
	temp = A13 * middleX + B13 * middleY + C13;
	if (temp < 0) {
		A13 = -A13;
		B13 = -B13;
		C13 = -C13;
	}

	//计算两条边分别于x轴和y轴平行的外接矩形
	int leftX = myMin(point1[0], point2[0], point3[0]);
	int rightX = myMax(point1[0], point2[0], point3[0]);
	int topY = myMax(point1[1], point2[1], point3[1]);
	int bottomY = myMin(point1[1], point2[1], point3[1]);

	//遍历矩形，处理后的一般方程，三角形中的任意一个点代入3条曲线，都会使Ax+By+C的结果都大于0
	for (int i = leftX; i <= rightX; i = i + 1) {
		for (int j = bottomY; j <= topY; j = j + 1) {
			if (A12 * i + B12 * j + C12 > 0 &&
				A23 * i + B23 * j + C23 > 0 &&
				A13 * i + B13 * j + C13 > 0) {
				//防止数组越界
				if ((*length) >= MAX_LENGTH - 3) break;

				vertices[(*length)++] = normalize(i);
				vertices[(*length)++] = normalize(j);
				vertices[(*length)++] = 0.0f;	
			}
		}
	}
}

void getCircleVertex(int originPointX, int originPointY, int radius, float vertices[MAX_LENGTH], int* length) {
	//移到原点，记录偏移量
	int moveX = originPointX;
	int moveY = originPointY;

	int x = 0;
	int y = radius;
	int end = (int)(radius * 1.0 / (float)sqrt(2));
	float d = 1.25 - radius;

	//加入初始点及对称的其它7个点
	while (x <= end) {
		//加入新点及对称的其它7个点
		vertices[(*length)++] = normalize(x + moveX);
		vertices[(*length)++] = normalize(y + moveY);
		vertices[(*length)++] = 0.0f;
		vertices[(*length)++] = normalize(y + moveX);
		vertices[(*length)++] = normalize(-x + moveY);
		vertices[(*length)++] = 0.0f;
		vertices[(*length)++] = normalize(-x + moveX);
		vertices[(*length)++] = normalize(-y + moveY);
		vertices[(*length)++] = 0.0f;
		vertices[(*length)++] = normalize(-y + moveX);
		vertices[(*length)++] = normalize(x + moveY);
		vertices[(*length)++] = 0.0f;
		vertices[(*length)++] = normalize(y + moveX);
		vertices[(*length)++] = normalize(x + moveY);
		vertices[(*length)++] = 0.0f;
		vertices[(*length)++] = normalize(x + moveX);
		vertices[(*length)++] = normalize(-y + moveY);
		vertices[(*length)++] = 0.0f;
		vertices[(*length)++] = normalize(-y + moveX);
		vertices[(*length)++] = normalize(-x + moveY);
		vertices[(*length)++] = 0.0f;
		vertices[(*length)++] = normalize(-x + moveX);
		vertices[(*length)++] = normalize(y + moveY);
		vertices[(*length)++] = 0.0f;	
		vertices[(*length)++] = normalize(x + moveX);
		vertices[(*length)++] = normalize(y + moveY);
		vertices[(*length)++] = 0.0f;
		
		
		if (d <= 0) {
			x = x + 1;
			y = y;
			d = d + 2 * x + 2;	
		}
		else {
			x = x + 1;
			y = y - 1;
			d = d + 2 * (x - y) + 5;
		}
	}
}

float normalize(int input) {
	float result = float(input) / WINDOW_SIZE;
	return result;
}

void initImGUI(GLFWwindow* window) {
	//创建并绑定ImGui
	const char* glsl_version = "#version 130";
	IMGUI_CHECKVERSION();
	ImGui::CreateContext();
	ImGuiIO& io = ImGui::GetIO(); (void)io;
	ImGui::StyleColorsDark();
	ImGui_ImplGlfw_InitForOpenGL(window, true);
	ImGui_ImplOpenGL3_Init(glsl_version);
}

void createAndRenderLineImGUI(GLFWwindow* window, bool *show_window, int point1[2], int point2[2], int point3[2], bool *isDrawLine) {
	//创建imgui
	ImGui_ImplOpenGL3_NewFrame();
	ImGui_ImplGlfw_NewFrame();
	ImGui::NewFrame();

	ImGui::Begin("Edit points", show_window, ImGuiWindowFlags_MenuBar);
	//ImGui::ColorEdit3("top color", (float*)&topColor);
	ImGui::Text("input 3 points");
	ImGui::SliderInt2("point1", point1, -180, 180);
	ImGui::SliderInt2("point2", point2, -180, 180);
	ImGui::SliderInt2("point3", point3, -180, 180);
	if (ImGui::Button("Draw Circle"))
		(*isDrawLine) = false;
	ImGui::End();

	ImGui::Render();
	int display_w, display_h;
	glfwMakeContextCurrent(window);
	glfwGetFramebufferSize(window, &display_w, &display_h);
	glViewport(0, 0, display_w, display_h);
	ImGui_ImplOpenGL3_RenderDrawData(ImGui::GetDrawData());
}

void createAndRenderCircleImGUI(GLFWwindow* window, bool *show_window, int point[2], int *radius, bool *isDrawLine) {
	//创建imgui
	ImGui_ImplOpenGL3_NewFrame();
	ImGui_ImplGlfw_NewFrame();
	ImGui::NewFrame();

	ImGui::Begin("Edit points", show_window, ImGuiWindowFlags_MenuBar);
	ImGui::Text("input 3 points");
	ImGui::SliderInt2("point1", point, -WINDOW_SIZE, WINDOW_SIZE);
	ImGui::SliderInt("point1", radius, 0, WINDOW_SIZE);
	if (ImGui::Button("Draw Line"))
		(*isDrawLine) = true;
	ImGui::End();

	ImGui::Render();
	int display_w, display_h;
	glfwMakeContextCurrent(window);
	glfwGetFramebufferSize(window, &display_w, &display_h);
	glViewport(0, 0, display_w, display_h);
	ImGui_ImplOpenGL3_RenderDrawData(ImGui::GetDrawData());
}

int myMin(int a, int b, int c) {
	if (a < b) {
		if (a < c) {
			return a;
		}
		else {
			return c;
		}
	}
	else {
		if (b < c) {
			return b;
		}
		else {
			return c;
		}
	}
}

int myMax(int a, int b, int c) {
	if (a > b) {
		if (a > c) {
			return a;
		}
		else {
			return c;
		}
	}
	else {
		if (b > c) {
			return b;
		}
		else {
			return c;
		}
	}
}
```
