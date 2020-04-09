---
title: GLUT+VS2017环境配置
date: 2018-08-05 17:15:05
tags:
    - OpenGL
    - Glut
---

参考了[AvatarForTest的博客：OpenGL+VS2017 环境配置](https://blog.csdn.net/AvatarForTest/article/details/79199807)

# 第一步
1. 下载VS2017
2. 下载glutdlls37beta.zip

<!-- more -->

# 第二步  
1. 解压glutdlls37beta.zip
2. 建立目录,将dll文件放入bin中，头文件放入include中，lib文件放入lib中  
![glut_menu.png](/img/opengl_img/glut_menu.png)

# 第三步
1. 打开VS2017，新建c++空项目  
![glut_1.png](/img/opengl_img/glut_1.png)
2. 点击“解决方案资源管理器”，右键点击源文件，添加新项，创建.cpp源文件  
![glut_2.png](/img/opengl_img/glut_2.png)
3. 右键点击项目，在弹出的选项中，单击 “属性”  
![glut_3.png](/img/opengl_img/glut_3.png)
4. 点击“VC++目录”，第二步会有下拉列表，单击“编辑”
![glut_4.png](/img/opengl_img/glut_4.png)
5. 点击添加头文件  
![glut_5.png](/img/opengl_img/glut_5.png)
6. 同样的道理，加入库文件  
![glut_6.png](/img/opengl_img/glut_6.png)
7. 包含的库文件VS还认不出来，我们需要指定一下，配置链接器，点击“编辑”
![glut_7.png](/img/opengl_img/glut_7.png)  
![glut_8.png](/img/opengl_img/glut_8.png)

# 第五步
在之前的main.cpp中添加如下代码，点击“运行”  
```
#include<iostream>
#include<GL/glut.h>

using namespace std;

void init(void) {
	glClearColor(1.0, 1.0, 1.0, 0.0);
	glMatrixMode(GL_PROJECTION);
	gluOrtho2D(0.0, 200.0, 0.0, 150.0);
}

void lineSegment(void) {
	glClear(GL_COLOR_BUFFER_BIT);
	glColor3f(0.0, 0.4, 0.2);
	glBegin(GL_LINES);
	glVertex2i(180, 15);
	glVertex2i(10, 145);
	glEnd();
	glFlush();
}

void main(int argc, char** argv) {
	glutInit(&argc, argv);
	glutInitDisplayMode(GLUT_SINGLE | GLUT_RGB);
	glutInitWindowPosition(50, 100);
	glutInitWindowSize(400, 300);
	glutCreateWindow("An Example OpenGL Program");

	init();
	glutDisplayFunc(lineSegment);
	glutMainLoop();
}
```
![glut_9.png](/img/opengl_img/glut_9.png)


