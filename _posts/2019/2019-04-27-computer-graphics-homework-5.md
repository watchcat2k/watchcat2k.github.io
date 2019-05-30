---
layout: post
title: 计算机图形学作业（ 五）：画一个立方体并实现 Phong Shading 和 Gouraud Shading 两种光照
date: 2019-04-27 00:00:00
categories: 
- CG-计算机图形学
tags: 
- 计算机图形学
- ImGUI
- OpenGL
- C++
- Shading
description: 画一个立方体并实现 Phong Shading 和 Gouraud Shading 两种光照。
---



# 颜色和光照场景	{#color-and-stage}
我们在现实生活中看到某一物体的颜色并不是这个物体真正拥有的颜色，而是它所反射的颜色。换句话说，那些不能被物体所吸收的颜色（被拒绝的颜色）就是我们能够感知到的物体的颜色。例如，太阳光能被看见的白光其实是由许多不同的颜色组合而成的（如下图所示）。如果我们将白光照在一个蓝色的玩具上，这个蓝色的玩具会吸收白光中除了蓝色以外的所有子颜色，不被吸收的蓝色光被反射到我们的眼中，让这个玩具看起来是蓝色的。下图显示的是一个珊瑚红的玩具，它以不同强度反射了多个颜色。

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-04/2019-04-26-1.png)

所以，在 OpenGL 中，我们要创建一个自然光源和一个物体，那么要计算物体的反射颜色，就将自然光源和物体这两个颜色的向量作分量相乘，如下：
```
glm::vec3 lightColor(1.0f, 1.0f, 1.0f);
glm::vec3 toyColor(1.0f, 0.5f, 0.31f);
glm::vec3 result = lightColor * toyColor; // = (1.0f, 0.5f, 0.31f);

```
如果自然光源不是白光，而是其它颜色光，那么物体反射出来的颜色也会作相应的改变。

为了模拟 OpenGL 中的颜色、阴影，我们要创建一个光照场景，包括一个自然光源和一个正方体，为我们后面的工作作准备。为了不产生混淆，自然光源和物体使用不同的VAO。结果如下：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-04/2019-04-26-2.png)

# Phong Shading
在 OpenGL 中，常用的一个光照模型被称为冯氏光照模型(Phong Lighting Model)。冯氏光照模型的主要结构由3个分量组成：环境(Ambient)、漫反射(Diffuse)和镜面(Specular)光照。下面这张图展示了这些光照分量看起来的样子：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-04/2019-04-26-3.png)

- 环境光照(Ambient Lighting)：即使在黑暗的情况下，世界上通常也仍然有一些光亮（月亮、远处的光），所以物体几乎永远不会是完全黑暗的。为了模拟这个，我们会使用一个环境光照常量，它永远会给物体一些颜色。
- 漫反射光照(Diffuse Lighting)：模拟光源对物体的方向性影响(Directional Impact)。它是冯氏光照模型中视觉上最显著的分量。物体的某一部分越是正对着光源，它就会越亮。
- 镜面光照(Specular Lighting)：模拟有光泽物体上面出现的亮点。镜面光照的颜色相比于物体的颜色会更倾向于光的颜色。

## 环境光照    {#ambient}
我们使用一个很小的常量（光照）颜色，添加到物体片段的最终颜色中，这样子的话即便场景中没有直接的光源也能看起来存在有一些发散的光。

把环境光照添加到场景里非常简单：我们用光的颜色乘以一个很小的常量环境因子，再乘以物体的颜色，然后将最终结果作为片段的颜色，片段着色器代码如下：
```
void main()
{
    float ambientStrength = 0.1;
    vec3 ambient = ambientStrength * lightColor;

    vec3 result = ambient * objectColor;
    FragColor = vec4(result, 1.0);
}

```

## 漫反射光照    {#diffuse}
漫反射光照使物体上与光线方向越接近的片段能从光源处获得更多的亮度。为了实现这一点，我们利用自然光源发出的光线的向量与物体表面法向量形成的夹角，计算物体表面的漫反射光照。

法向量是一个垂直于顶点表面的向量，我们通过顶点着色器直接传入立方体六个面对应的法向量的值，顶点数据可以在[官方资源](https://learnopengl.com/code_viewer.php?code=lighting/basic_lighting_vertex_data)中找到。要注意的是，当物体进行了放缩、旋转等操作时，我们在顶点着色器传入的法向量值就变得不准确了，所以我们要在顶点着色器对法向量进行处理，以保证准确性，如下：
```
Normal = mat3(transpose(inverse(model))) * aNormal;

```
最后，在片段着色器重，就可以进行漫反射光照的计算了：
```
//漫反射
 vec3 norm = normalize(Normal);
 vec3 lightDir = normalize(lightPos - FragPos);
 float diff = max(dot(norm, lightDir), 0.0);
 vec3 diffuse = diffuseStrength * diff * lightColor;
 
```

## 镜面光照    {#specular}
镜面光照也是依据光的方向向量和物体的法向量来决定的，但是它也依赖于观察方向，例如玩家是从什么方向看着这个片段的。镜面光照是基于光的反射特性。如果我们想象物体表面像一面镜子一样，那么，无论我们从哪里去看那个表面所反射的光，镜面光照都会达到最大化。

所以比起漫反射光照，我们需要多一个观察者的位置变量，然后便可进行镜面光照计算，如下：
```
//镜面光
 vec3 viewDir = normalize(viewPos - FragPos);
 vec3 reflectDir = reflect(-lightDir, norm);  
 float spec = pow(max(dot(viewDir, reflectDir), 0.0), ShininessStrength);
 vec3 specular = specularStrength * spec * lightColor;  
 
```

## 着色器代码    {#phong-shader}
顶点着色器：
```
#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aNormal;

out vec3 FragPos;
out vec3 Normal;

uniform mat4 model;
uniform mat4 view;
uniform mat4 projection;

void main()
{
    FragPos = vec3(model * vec4(aPos, 1.0));
    Normal = mat3(transpose(inverse(model))) * aNormal;  
    
    gl_Position = projection * view * vec4(FragPos, 1.0);
}

```

片段着色器：
```
#version 330 core

out vec4 FragColor;

in vec3 Normal;  
in vec3 FragPos;  

uniform float ambientStrength;
uniform float diffuseStrength;
uniform float specularStrength; 
uniform int ShininessStrength;

uniform vec3 lightPos; 
uniform vec3 objectColor;
uniform vec3 lightColor;
uniform vec3 viewPos; 

void main() {
	//环境光
    vec3 ambient = ambientStrength * lightColor;

	//漫反射
	vec3 norm = normalize(Normal);
	vec3 lightDir = normalize(lightPos - FragPos);
	float diff = max(dot(norm, lightDir), 0.0);
	vec3 diffuse = diffuseStrength * diff * lightColor;

	//镜面光
	vec3 viewDir = normalize(viewPos - FragPos);
	vec3 reflectDir = reflect(-lightDir, norm);  
	float spec = pow(max(dot(viewDir, reflectDir), 0.0), ShininessStrength);
	vec3 specular = specularStrength * spec * lightColor;  

	vec3 result = (ambient + diffuse + specular) * objectColor;
    FragColor = vec4(result, 1.0);
}

```

## 结果    {#phong-result}
最终，基于Phong Shading 模型的效果图如下：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-04/2019-04-26-4.png)

# Gouraud Shading
Phong Shading 是在片段着色器实现冯氏光照模型，而Gouraud Shading是在顶点着色器实现的冯氏光照模型。所以要实现 Gouraud Shading，只需要修改两个着色器的代码，把片段着色器中关于冯氏光照模型的计算移到顶点着色器即可。

在顶点着色器中做光照的优势是，相比片段来说，顶点要少得多，因此会更高效，所以（开销大的）光照计算频率会更低。然而，顶点着色器中的最终颜色值是仅仅只是那个顶点的颜色值，片段的颜色值是由插值光照颜色所得来的。结果就是这种光照看起来不会非常真实，除非使用了大量顶点。

## 着色器代码    {#gouraud-shader}
顶点着色器：
```
#version 330 core
layout (location = 0) in vec3 aPos;
layout (location = 1) in vec3 aNormal;

out vec3 LightingColor; 

uniform float ambientStrength;
uniform float diffuseStrength;
uniform float specularStrength; 
uniform int ShininessStrength;

uniform vec3 lightPos;
uniform vec3 viewPos;
uniform vec3 lightColor;

uniform mat4 model;
uniform mat4 view;
uniform mat4 projection;

void main()
{
    gl_Position = projection * view * model * vec4(aPos, 1.0);
    
    // gouraud shading
    vec3 Position = vec3(model * vec4(aPos, 1.0));
    vec3 Normal = mat3(transpose(inverse(model))) * aNormal;
    
    // 环境光
    vec3 ambient = ambientStrength * lightColor;
  	
    // 漫反射 
    vec3 norm = normalize(Normal);
    vec3 lightDir = normalize(lightPos - Position);
    float diff = max(dot(norm, lightDir), 0.0);
    vec3 diffuse = diffuseStrength * diff * lightColor;
    
    // 镜面光
    vec3 viewDir = normalize(viewPos - Position);
    vec3 reflectDir = reflect(-lightDir, norm);  
    float spec = pow(max(dot(viewDir, reflectDir), 0.0), ShininessStrength);
    vec3 specular = specularStrength * spec * lightColor;      

    LightingColor = ambient + diffuse + specular;
}

```

片段着色器：
```
#version 330 core
out vec4 FragColor;

in vec3 LightingColor; 

uniform vec3 objectColor;

void main()
{
   FragColor = vec4(LightingColor * objectColor, 1.0);
}

```

## 结果    {#gouraud}
最终基于 Gouraud Shading 模型的效果图如下：

![](https://gitee.com/watchcat2k/pictures_base/raw/master/2019-04/2019-04-26-5.png)

# 光源来回移动（加分项）    {#light-tanslate}
要实现光源的来回移动并不难，我先定义了光源的坐标，如下：
```
glm::vec3 lightPos(20.0f, 8.0f, 20.0f);

```
然后在绘图的循环中不断控制 light.x 的增减，即可实现观察者左右来回移动，如下：
```
//灯光左右移动
if (enable_translate) {
	if (is_translate_to_left) {
		lightPos.x = lightPos.x - 0.5f;
		if (lightPos.x < -30) {
			is_translate_to_left = false;
		}
	}
	else {
		lightPos.x = lightPos.x + 0.5f;
		if (lightPos.x > 30) {
			is_translate_to_left = true;
		}
	}	
}
		
```

# 代码    {#code}
写代码时，为了方便地使用shader，我使用了[官方的shader类库](https://learnopengl.com/code_viewer_gh.php?code=includes/learnopengl/shader.h)，引入该文件后，便可以方便地创建和使用shader。
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

//使用了官方的shader库文件，可以更方便地操作shader
//注意，使用官方库操作shadder时，自己写的顶点和片段着色器代码必须另外写成文件
//然后把文件路径传入shader.h提供的构造函数
#include "shader.h"  
#include "camera.h"

using namespace std;

const int WINDOW_WIDTH = 1000;
const int WINDOW_HEIGHT = 800;

void framebuffer_size_callback(GLFWwindow* window, int width, int height);
void mouse_callback(GLFWwindow* window, double xpos, double ypos);
void scroll_callback(GLFWwindow* window, double xoffset, double yoffset);
void processInput(GLFWwindow* window);

//camera的位置与后面物体的view向量相反
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

	//正方体边长为2.0f*2s
	float half_edge = 2.0f;
	glm::vec3 verticalVec1 = glm::vec3(0.0f, 0.0f, -1.0f);
	glm::vec3 verticalVec2 = glm::vec3(0.0f, 0.0f, 1.0f);
	glm::vec3 verticalVec3 = glm::vec3(-1.0f, 0.0f, 0.0f);
	glm::vec3 verticalVec4 = glm::vec3(1.0f, 0.0f, 0.0f);
	glm::vec3 verticalVec5 = glm::vec3(0.0f, -1.0f, 0.0f);
	glm::vec3 verticalVec6 = glm::vec3(0.0f, 1.0f, 0.0f);

	float vertices[] = {
	-half_edge, -half_edge, -half_edge, verticalVec1.x, verticalVec1.y, verticalVec1.z,
	 half_edge, -half_edge, -half_edge, verticalVec1.x, verticalVec1.y, verticalVec1.z,
	 half_edge,  half_edge, -half_edge, verticalVec1.x, verticalVec1.y, verticalVec1.z,
	 half_edge,  half_edge, -half_edge, verticalVec1.x, verticalVec1.y, verticalVec1.z,
	-half_edge,  half_edge, -half_edge, verticalVec1.x, verticalVec1.y, verticalVec1.z,
	-half_edge, -half_edge, -half_edge, verticalVec1.x, verticalVec1.y, verticalVec1.z,

	-half_edge, -half_edge,  half_edge, verticalVec2.x, verticalVec2.y, verticalVec2.z,
	 half_edge, -half_edge,  half_edge, verticalVec2.x, verticalVec2.y, verticalVec2.z,
	 half_edge,  half_edge,  half_edge, verticalVec2.x, verticalVec2.y, verticalVec2.z,
	 half_edge,  half_edge,  half_edge, verticalVec2.x, verticalVec2.y, verticalVec2.z,
	-half_edge,  half_edge,  half_edge, verticalVec2.x, verticalVec2.y, verticalVec2.z,
	-half_edge, -half_edge,  half_edge, verticalVec2.x, verticalVec2.y, verticalVec2.z,

	-half_edge,  half_edge,  half_edge, verticalVec3.x, verticalVec3.y, verticalVec3.z,
	-half_edge,  half_edge, -half_edge, verticalVec3.x, verticalVec3.y, verticalVec3.z,
	-half_edge, -half_edge, -half_edge, verticalVec3.x, verticalVec3.y, verticalVec3.z,
	-half_edge, -half_edge, -half_edge, verticalVec3.x, verticalVec3.y, verticalVec3.z,
	-half_edge, -half_edge,  half_edge, verticalVec3.x, verticalVec3.y, verticalVec3.z,
	-half_edge,  half_edge,  half_edge, verticalVec3.x, verticalVec3.y, verticalVec3.z,

	 half_edge,  half_edge,  half_edge, verticalVec4.x, verticalVec4.y, verticalVec4.z,
	 half_edge,  half_edge, -half_edge, verticalVec4.x, verticalVec4.y, verticalVec4.z,
	 half_edge, -half_edge, -half_edge, verticalVec4.x, verticalVec4.y, verticalVec4.z,
	 half_edge, -half_edge, -half_edge, verticalVec4.x, verticalVec4.y, verticalVec4.z,
	 half_edge, -half_edge,  half_edge, verticalVec4.x, verticalVec4.y, verticalVec4.z,
	 half_edge,  half_edge,  half_edge, verticalVec4.x, verticalVec4.y, verticalVec4.z,

	-half_edge, -half_edge, -half_edge, verticalVec5.x, verticalVec5.y, verticalVec5.z,
	 half_edge, -half_edge, -half_edge, verticalVec5.x, verticalVec5.y, verticalVec5.z,
	 half_edge, -half_edge,  half_edge, verticalVec5.x, verticalVec5.y, verticalVec5.z,
	 half_edge, -half_edge,  half_edge, verticalVec5.x, verticalVec5.y, verticalVec5.z,
	-half_edge, -half_edge,  half_edge, verticalVec5.x, verticalVec5.y, verticalVec5.z,
	-half_edge, -half_edge, -half_edge, verticalVec5.x, verticalVec5.y, verticalVec5.z,

	-half_edge,  half_edge, -half_edge, verticalVec6.x, verticalVec6.y, verticalVec6.z,
	 half_edge,  half_edge, -half_edge, verticalVec6.x, verticalVec6.y, verticalVec6.z,
	 half_edge,  half_edge,  half_edge, verticalVec6.x, verticalVec6.y, verticalVec6.z,
	 half_edge,  half_edge,  half_edge, verticalVec6.x, verticalVec6.y, verticalVec6.z,
	-half_edge,  half_edge,  half_edge, verticalVec6.x, verticalVec6.y, verticalVec6.z,
	-half_edge,  half_edge, -half_edge, verticalVec6.x, verticalVec6.y, verticalVec6.z,
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

	//物体（正方体）的VAO
	unsigned int objectVAO;
	unsigned int VBO;
	unsigned int EBO;

	//必须先绑定VA0
	glGenVertexArrays(1, &objectVAO);
	glBindVertexArray(objectVAO);

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

	//法向量属性
	//属性位置值为0的顶点属性，有3个值，顶点着色器总参数大小为6个float，位置属性偏移为3个float
	glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)(3 * sizeof(float)));
	glEnableVertexAttribArray(1);

	//灯光VAO
	unsigned int lightVAO;
	glGenVertexArrays(1, &lightVAO);
	glBindVertexArray(lightVAO);

	glBindBuffer(GL_ARRAY_BUFFER, VBO);

	glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);

	//顶点着色器总参数大小为6个float，是为了与前面的objectVAO保持一致
	glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 6 * sizeof(float), (void*)0);
	glEnableVertexAttribArray(0);

	float scaleDelta = 1.0f;
	float rotateAngle = glm::radians(15.0f);
	float perspective_fov = 45.0f;
	float perspective_division = (float)WINDOW_WIDTH / (float)WINDOW_HEIGHT;
	float perspective_near = 0.1f;
	float perspective_far = 100.0f;
	bool enable_camera = false;
	bool is_phong_shader = true;
	bool is_gouraud_shader = false;
	bool enable_translate = false;
	bool is_translate_to_left = true;
	bool show_window = true;
	float ambientStrength = 0.1f;
	float diffuseStrength = 1.0f;
	float specularStrength = 1.0f;
	int ShininessStrength = 32;
	glm::vec3 lightPos(20.0f, 8.0f, 20.0f);
	glm::vec3 viewPos;

	while (!glfwWindowShouldClose(window)) {
		if (enable_camera) {
			//告诉glfw获取我们的鼠标
			glfwSetInputMode(window, GLFW_CURSOR, GLFW_CURSOR_DISABLED);
		}
		else {
			glfwSetInputMode(window, GLFW_CURSOR, GLFW_CURSOR_NORMAL);
		}
	
		//计算当前时间和帧间隔时间，为了使移动更平滑
		float currentFrame = glfwGetTime();
		deltaTime = currentFrame - lastFrame;
		lastFrame = currentFrame;

		processInput(window);

		//清除屏幕
		glClearColor(0.1f, 0.1f, 0.1f, 1.0f);
		glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

		//创建坐标变换
		glm::mat4 model = glm::mat4(1.0f);
		glm::mat4 view = glm::mat4(1.0f);
		glm::mat4 projection = glm::mat4(1.0f);
		
		if (enable_camera) {
			view = camera.GetViewMatrix();
			viewPos = camera.Position;
			
			//它的第一个参数定义了fov的值，它表示的是视野，并且设置了观察空间的大小。第二个参数设置了宽高比。第三和第四个参数设置了平截头体的近和远平面
			//glm::radians()是把角度转为弧度
			//利用鼠标放缩
			projection = glm::perspective(glm::radians(camera.Zoom), perspective_division, perspective_near, perspective_far);

		}
		else {
			//把场景移动(-1.0f, -1.0f, -5.0f)
			view = glm::translate(view, glm::vec3(0.0f, 0.0f, -10.0f));
			viewPos = glm::vec3(1.0f, 1.0f, 5.0f);
			
			projection = glm::perspective(glm::radians(perspective_fov), perspective_division, perspective_near, perspective_far);
		}

		//灯光左右移动
		if (enable_translate) {
			if (is_translate_to_left) {
				lightPos.x = lightPos.x - 0.5f;
				if (lightPos.x < -30) {
					is_translate_to_left = false;
				}
			}
			else {
				lightPos.x = lightPos.x + 0.5f;
				if (lightPos.x > 30) {
					is_translate_to_left = true;
				}
			}	
		}

		//利用官方库shader.h构造着色器
		if (is_phong_shader) {
			Shader objectShader("objectVertexPhongShader.vs", "objectFragmentPhongShader.fs");
			//使用着色器程序
			objectShader.use();
			//设置着色器程序uniform
			objectShader.setFloat("ambientStrength", ambientStrength);
			objectShader.setFloat("diffuseStrength", diffuseStrength);
			objectShader.setFloat("specularStrength", specularStrength);
			objectShader.setInt("ShininessStrength", ShininessStrength);

			objectShader.setVec3("objectColor", 1.0f, 0.5f, 0.31f);
			objectShader.setVec3("lightColor", 1.0f, 1.0f, 1.0f);
			objectShader.setVec3("lightPos", lightPos);
			objectShader.setVec3("viewPos", viewPos);

			objectShader.setMat4("view", view);
			objectShader.setMat4("projection", projection);
			//model包含了位移、缩放与旋转操作
			model = glm::mat4(1.0f);
			model = glm::rotate(model, rotateAngle, glm::vec3(0.0f, 1.0f, 0.0f));
			objectShader.setMat4("model", model);

			//画物体
			glBindVertexArray(objectVAO);
			glDrawElements(GL_TRIANGLES, 36, GL_UNSIGNED_INT, 0);
		}
		else if (is_gouraud_shader) {
			Shader objectShader("objectVertexGouraudShader.vs", "objectFragmentGouraudShader.fs");
			//使用着色器程序
			objectShader.use();
			//设置着色器程序uniform
			objectShader.setFloat("ambientStrength", ambientStrength);
			objectShader.setFloat("diffuseStrength", diffuseStrength);
			objectShader.setFloat("specularStrength", specularStrength);
			objectShader.setInt("ShininessStrength", ShininessStrength);

			objectShader.setVec3("objectColor", 1.0f, 0.5f, 0.31f);
			objectShader.setVec3("lightColor", 1.0f, 1.0f, 1.0f);
			objectShader.setVec3("lightPos", lightPos);
			objectShader.setVec3("viewPos", viewPos);

			objectShader.setMat4("view", view);
			objectShader.setMat4("projection", projection);
			//model包含了位移、缩放与旋转操作
			model = glm::mat4(1.0f);
			model = glm::rotate(model, rotateAngle, glm::vec3(0.0f, 1.0f, 0.0f));
			objectShader.setMat4("model", model);

			//画物体
			glBindVertexArray(objectVAO);
			glDrawElements(GL_TRIANGLES, 36, GL_UNSIGNED_INT, 0);
		}

		Shader lightShader("lightVertexShader.vs", "lightFragmentShader.fs");

		lightShader.use();
		lightShader.setMat4("view", view);
		lightShader.setMat4("projection", projection);
		model = glm::mat4(1.0f);
		model = glm::scale(model, glm::vec3(0.2f));
		model = glm::translate(model, lightPos);
		lightShader.setMat4("model", model);

		//画灯光
		glBindVertexArray(lightVAO);
		glDrawElements(GL_TRIANGLES, 36, GL_UNSIGNED_INT, 0);

		//创建imgui
		ImGui_ImplOpenGL3_NewFrame();
		ImGui_ImplGlfw_NewFrame();
		ImGui::NewFrame();

		ImGui::Begin("Edit cube", &show_window, ImGuiWindowFlags_MenuBar);

		if (ImGui::Button("enable camera")) {
			if (enable_camera) {
				enable_camera = false;
			}
			else {
				enable_camera = true;
			}
		}
		
		if (ImGui::RadioButton("Phong Shader", is_phong_shader)) {
			is_gouraud_shader = false;
			is_phong_shader = true;
		}
		if (ImGui::RadioButton("Gouraud Shader", is_gouraud_shader)) {
			is_gouraud_shader = true;
			is_phong_shader = false;
		}
		ImGui::SliderFloat("ambientStrength", &ambientStrength, 0.01f, 3.0f);
		ImGui::SliderFloat("diffuseStrength", &diffuseStrength, 0.01f, 3.0f);
		ImGui::SliderFloat("specularStrength", &specularStrength, 0.01f, 5.0f);
		ImGui::SliderInt("ShininessStrength", &ShininessStrength, 0, 256);
		
		if (ImGui::Button("enable translate")) {
			if (enable_translate) {
				enable_translate = false;
			}
			else {
				enable_translate = true;
			}
		}

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

	glDeleteVertexArrays(1, &objectVAO);
	glDeleteVertexArrays(1, &lightVAO);
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
