---
title: C++貪食蛇小遊戲
date: 2022-05-29 13:44:29
tags: Game
categories: [C++,實務開發]
top_img: /img/cover/008.jpg
cover: /img/cover/008.jpg
---
# 前言

用deque模擬貪食蛇身體，剩下的就是一些實作

# 程式碼

```cpp
#include <bits/stdc++.h>
#include <windows.h>
#include <conio.h>

const int width = 60,height = 28;

using namespace std;


char input;
bool running;
int score;

struct FOOD{
	int x,y;
};

struct SNACK{
	int x,y;
};

FOOD food;

deque<SNACK>snack;

void to(int x,int y){
	COORD pos;
	HANDLE hOutput;
    pos.X = x;
    pos.Y = y;
    hOutput = GetStdHandle(STD_OUTPUT_HANDLE);
    SetConsoleCursorPosition(hOutput, pos);
}

void print(){
	for(int i = 0;i<snack.size();i++){
		to(snack[i].x,snack[i].y);
		cout<<"■";
	}
}

void create_food(){
	while(true){
		bool flag = 1;
		food.x = (rand()%(width-1))+1;
		food.y = (rand()%(height-1))+1;
		if(food.x%2!=0)continue;
		for(int i = 0;i<snack.size();i++){
			if(food.x==snack[i].x and food.y==snack[i].y){
				flag = 0;
				break;
			}
		}
		if(flag==1)break;
	}
	to(food.x,food.y);
	cout<<"■";
}

void setup(){
	running = 1;
	score = 0;
	input = 'w';
	int dx[] = {2,0,-2,0};
	int dy[] = {0,1,0,-1};
	int x = 0,y = 0;
	//set screen
	for(int i = 0;i<4;){
		to(x,y);
		cout<<"■";
		if(x+dx[i]>width or y+dy[i]>height or x+dx[i]<0 or y+dy[i]<0){
			i++;
			continue;
		}
		x+=dx[i],y+=dy[i];
	}
	//set snack
	for(int i = 0;i<=10;i+=2){
		snack.push_back({24+i,20});
	}
	print();
	to(80,12);
	cout<<"Score = "<<score;
	create_food();	
}

void get_input(){
	if(input!='s' and GetAsyncKeyState(VK_UP))input = 'w';
	if(input!='w' and GetAsyncKeyState(VK_DOWN))input = 's';
	if(input!='a' and GetAsyncKeyState(VK_RIGHT))input = 'd';
	if(input!='d' and GetAsyncKeyState(VK_LEFT))input = 'a';
}

int check(int x,int y){//check snack
	if(x==0 or x==width or y==0 or y==height){
		return 0;
	}
	for(int i = 0;i<snack.size();i++){
		if(snack[i].x==x and snack[i].y==y){
			return 0;
		}
	}
	return 1;
}

void update_snack(){
	int now_x = snack.front().x;
	int now_y = snack.front().y;
	if(input=='w')now_y--;
	if(input=='a')now_x-=2;
	if(input=='s')now_y++;
	if(input=='d')now_x+=2;
	if(check(now_x,now_y)==0){
		running = 0;
		return;
	}
	snack.push_front({now_x,now_y});
	if(snack.front().x==food.x and snack.front().y==food.y){
		to(88,12);
		score++;
		cout<<score;
		create_food();
	}
	else{
		to(snack.back().x,snack.back().y);
		cout<<"  ";
		snack.pop_back();
	}
}

int main(){
	srand(time(NULL));
	system("chcp 65001");
	setup();
	while(running){
		get_input();
		update_snack();
		print();
		Sleep(100);
	}
	to(80,14);
	cout<<"Game Over!";
	to(80,16);
	system("pause");
}
```