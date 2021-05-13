//空気抵抗を加味し、打ち上げ花火の軌道をシミュレーションしました。
//地球以外の物体同士の万有引力は考慮しませんでした。
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <math.h>
//#include "physics2.h"
typedef struct condition
{
  const int width; // 見えている範囲の幅
  const int height; // 見えている範囲の高さ
  const double G; // 重力定数
  const double dt; // シミュレーションの時間幅
  const double cor; // 壁の反発係数
  const double res; //空気抵抗
} Condition;

// 個々の物体を表す構造体
typedef struct object
{
  double m;
  double x;
  double y;
  double vx;
  double vy;
  int n;  //地球含めた物体の数
  int open;  //開花後1になる
} Object;
void my_update_velocities(Object objects[], size_t objnum, const Condition cond){
  for (int i=1; i<objects[0].n; i++){
      double ay = 0; //加速度
      ay = (cond.G*objects[0].m/((objects[i].y-objects[0].y)*(objects[i].y-objects[0].y)))-cond.res*objects[i].vy;  //空気抵抗を加味
      objects[i].vy = ay*cond.dt+objects[i].vy;
  }
  double by = 0;  //加速度
  for (int i = 1; i<objects[0].n; i++){
    by = by-1*cond.G*objects[i].m/((objects[0].y-objects[i].y)*(objects[0].y-objects[i].y));
  }
  objects[0].vy = by*cond.dt+objects[0].vy;
}
void my_update_positions(Object objects[], size_t objnum, const Condition cond){
  objects[0].x = objects[0].vx*cond.dt+objects[0].x;
  objects[0].y = objects[0].vy*cond.dt+objects[0].y;
  for (int i=1; i<objects[0].n; i++){
    objects[i].x = objects[i].vx*cond.dt+objects[i].x;
    objects[i].y = objects[i].vy*cond.dt+objects[i].y;
  }
}
void my_plot_objects(Object objects[], size_t objnum, int t, Condition cond){
  int a = cond.width/2; //j==aの時原点
  int b = cond.height/2;  //i==bの時原点
  int c = objects[1].y;
  int e = objects[1].x;
  int check=0;
  
  //上の壁
  printf("+");
  for (int x = 0 ; x < cond.width ; x++)
    printf("-");
  printf("+\n");
  for (int i=0; i<cond.height; i++){
    printf("|");
    for (int j=0; j<cond.width; j++){
      if (objects[0].open==0&&i==b+c&&j==a+e){
        printf("\e[33m%c\e[0m", 'o');
      }else if (objects[0].open==1){
        for (int l=1; l<objects[0].n; l++){
            int f = objects[l].y;
            int g = objects[l].x;
            if(i==b+f&&j==a+g){
                if (l<145){
                    printf("\e[91m%c\e[0m", '*');
                }
                else if (l<289){
                    printf("\e[95m%c\e[0m", '*');
                }else{
                    printf("\e[93m%c\e[0m", '*');
                }
                check++;
            }
            if (check!=0)
                break;
        }
        if(check==0){
            printf(" ");
        }
        check=0;
      }else{
        printf(" ");
      }
    }
    printf("|\n");
  }
  // 下の壁
  printf("+");
  for (int x = 0 ; x < cond.width ; x++)
    printf("-");
  printf("+\n");
  double d = t;
  printf("t =  %.1f, objs[1].y =   %.2f, objs[1].x =  %.2f objs[1].vy = %.2f objs[1].vx = %.2f", d, objects[1].y, objects[1].x, objects[1].vy, objects[1].vx);
}


int main(int argc, char **argv)
{
  const Condition cond = {
		    .width  = 75,
		    .height = 50,
		    .G = 1.0,
		    .dt = 1.0,
		    .cor = 0.8,
            .res = 0.05
  };
  
  size_t objnum = 500;
  Object objects[objnum];

  // objects[0] は巨大な物体を画面外に... 地球のようなものを想定
  objects[1] = (Object){ .m = 60.0, .x = 0, .vx = 0, .y = cond.height/2, .vy = -5}; //火球
  objects[0] = (Object){ .m = 100000.0, .vx=0,  .y =  1000.0, .vy = 0.0, .n=2, .open=0};
  objects[2] = (Object){ .m = 60.0, .x = 30, .vx = 1, .y = -19.9, .vy = 2.0};


  // シミュレーション. ループは整数で回しつつ、実数時間も更新する
  //const double stop_time = 400;
  double t = 0;
  printf("\n");
  int i=0;
  while (objects[1].vy<0){
    t = i * cond.dt;
    i++;
    my_update_velocities(objects, objnum, cond);
    my_update_positions(objects, objnum, cond);
    
    // 表示の座標系は width/2, height/2 のピクセル位置が原点となるようにする
    my_plot_objects(objects, objnum, t, cond);
    
    usleep(200 * 1000); // 200 x 1000us = 200 ms ずつ停止
    printf("\e[%dA", cond.height+2);// 壁とパラメータ表示分で2行
  }
  double setposition = objects[1].y; //火球の停止位置
  objects[0].n=313;
  objects[0].open=1;
  objects[1] = (Object){ .m = 60.0, .x = 0, .vx = -0.5, .y = setposition, .vy = 0.0};
  objects[2] = (Object){ .m = 60.0, .x = 0, .vx = 0.5, .y = setposition, .vy = 0.0};
  objects[3] = (Object){ .m = 60.0, .x = 0, .vx = 0.5/sqrt(2), .y = setposition, .vy = 0.5/sqrt(2)};
  objects[4] = (Object){ .m = 60.0, .x = 0, .vx = -0.5/sqrt(2), .y = setposition, .vy = 0.5/sqrt(2)};
  objects[5] = (Object){ .m = 60.0, .x = 0, .vx = 0.5/sqrt(2), .y = setposition, .vy = -0.5/sqrt(2)};
  objects[6] = (Object){ .m = 60.0, .x = 0, .vx = -0.5/sqrt(2), .y = setposition, .vy = -0.5/sqrt(2)};
  objects[7] = (Object){ .m = 60.0, .x = 0, .vx = 1/4, .y = setposition, .vy = sqrt(3)/4};
  objects[8] = (Object){ .m = 60.0, .x = 0, .vx = -1/4, .y = setposition, .vy = -sqrt(3)/4};
  objects[9] = (Object){ .m = 60.0, .x = 0, .vx = 1/4, .y = setposition, .vy = -sqrt(3)/4};
  objects[10] = (Object){ .m = 60.0, .x = 0, .vx = -1/4, .y = setposition, .vy = sqrt(3)/4};
  objects[11] = (Object){ .m = 60.0, .x = 0, .vx = sqrt(3)/4, .y = setposition, .vy = 1/4};
  objects[12] = (Object){ .m = 60.0, .x = 0, .vx = -sqrt(3)/4, .y = setposition, .vy = -1/4};
  objects[13] = (Object){ .m = 60.0, .x = 0, .vx = sqrt(3)/4, .y = setposition, .vy = -1/4};
  objects[14] = (Object){ .m = 60.0, .x = 0, .vx = -sqrt(3)/4, .y = setposition, .vy = 1/4};
  objects[15] = (Object){ .m = 60.0, .x = 0, .vx = (sqrt(6)-sqrt(2))/8, .y = setposition, .vy = (sqrt(6)+sqrt(2))/8};
  objects[16] = (Object){ .m = 60.0, .x = 0, .vx = -(sqrt(6)-sqrt(2))/8, .y = setposition, .vy = -(sqrt(6)+sqrt(2))/8};
  objects[17] = (Object){ .m = 60.0, .x = 0, .vx = (sqrt(6)-sqrt(2))/8, .y = setposition, .vy = -(sqrt(6)+sqrt(2))/8};
  objects[18] = (Object){ .m = 60.0, .x = 0, .vx = -(sqrt(6)-sqrt(2))/8, .y = setposition, .vy = (sqrt(6)+sqrt(2))/8};
  objects[19] = (Object){ .m = 60.0, .x = 0, .vx = (sqrt(6)+sqrt(2))/8, .y = setposition, .vy = (sqrt(6)-sqrt(2))/8};
  objects[20] = (Object){ .m = 60.0, .x = 0, .vx = -(sqrt(6)+sqrt(2))/8, .y = setposition, .vy = -(sqrt(6)-sqrt(2))/8};
  objects[21] = (Object){ .m = 60.0, .x = 0, .vx = (sqrt(6)+sqrt(2))/8, .y = setposition, .vy = -(sqrt(6)-sqrt(2))/8};
  objects[22] = (Object){ .m = 60.0, .x = 0, .vx = -(sqrt(6)+sqrt(2))/8, .y = setposition, .vy = (sqrt(6)-sqrt(2))/8};
  objects[23] = (Object){ .m = 60.0, .x = 0, .vx = 0, .y = setposition, .vy = 0.5};
  objects[24] = (Object){ .m = 60.0, .x = 0, .vx = 0, .y = setposition, .vy = 0.5};
  objects[25] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*sin(M_PI/36), .y = setposition, .vy = 0.5*cos(M_PI/36)};
  objects[26] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*sin(M_PI/36), .y = setposition, .vy = -0.5*cos(M_PI/36)};
  objects[27] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*sin(M_PI/36), .y = setposition, .vy = -0.5*cos(M_PI/36)};
  objects[28] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*sin(M_PI/36), .y = setposition, .vy = 0.5*cos(M_PI/36)};
  objects[29] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*cos(M_PI/36), .y = setposition, .vy = 0.5*sin(M_PI/36)};
  objects[30] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*cos(M_PI/36), .y = setposition, .vy = -0.5*sin(M_PI/36)};
  objects[31] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*cos(M_PI/36), .y = setposition, .vy = -0.5*sin(M_PI/36)};
  objects[32] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*cos(M_PI/36), .y = setposition, .vy = 0.5*sin(M_PI/36)};
  objects[33] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*cos(M_PI/18), .y = setposition, .vy = 0.5*sin(M_PI/18)};
  objects[34] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*cos(M_PI/18), .y = setposition, .vy = -0.5*sin(M_PI/18)};
  objects[35] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*cos(M_PI/18), .y = setposition, .vy = -0.5*sin(M_PI/18)};
  objects[36] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*cos(M_PI/18), .y = setposition, .vy = 0.5*sin(M_PI/18)};
  objects[37] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*sin(M_PI/18), .y = setposition, .vy = 0.5*cos(M_PI/18)};
  objects[38] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*sin(M_PI/18), .y = setposition, .vy = -0.5*cos(M_PI/18)};
  objects[39] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*sin(M_PI/18), .y = setposition, .vy = -0.5*cos(M_PI/18)};
  objects[40] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*sin(M_PI/18), .y = setposition, .vy = 0.5*cos(M_PI/18)};
  objects[41] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*sin(M_PI/9), .y = setposition, .vy = 0.5*cos(M_PI/9)};
  objects[42] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*sin(M_PI/9), .y = setposition, .vy = -0.5*cos(M_PI/9)};
  objects[43] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*sin(M_PI/9), .y = setposition, .vy = -0.5*cos(M_PI/9)};
  objects[44] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*sin(M_PI/9), .y = setposition, .vy = 0.5*cos(M_PI/9)};
  objects[45] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*cos(M_PI/9), .y = setposition, .vy = 0.5*sin(M_PI/9)};
  objects[46] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*cos(M_PI/9), .y = setposition, .vy = -0.5*sin(M_PI/9)};
  objects[47] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*cos(M_PI/9), .y = setposition, .vy = -0.5*sin(M_PI/9)};
  objects[48] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*cos(M_PI/9), .y = setposition, .vy = 0.5*sin(M_PI/9)};
  objects[49] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*sin(5*M_PI/36), .y = setposition, .vy = 0.5*cos(5*M_PI/36)};
  objects[50] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*sin(5*M_PI/36), .y = setposition, .vy = -0.5*cos(5*M_PI/36)};
  objects[51] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*sin(5*M_PI/36), .y = setposition, .vy = -0.5*cos(5*M_PI/36)};
  objects[52] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*sin(5*M_PI/36), .y = setposition, .vy = 0.5*cos(5*M_PI/36)};
  objects[53] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*cos(5*M_PI/36), .y = setposition, .vy = 0.5*sin(5*M_PI/36)};
  objects[54] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*cos(5*M_PI/36), .y = setposition, .vy = -0.5*sin(5*M_PI/36)};
  objects[55] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*cos(5*M_PI/36), .y = setposition, .vy = -0.5*sin(5*M_PI/36)};
  objects[56] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*cos(5*M_PI/36), .y = setposition, .vy = 0.5*sin(5*M_PI/36)};
  objects[57] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*sin(7*M_PI/36), .y = setposition, .vy = 0.5*cos(7*M_PI/36)};
  objects[58] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*sin(7*M_PI/36), .y = setposition, .vy = -0.5*cos(7*M_PI/36)};
  objects[59] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*sin(7*M_PI/36), .y = setposition, .vy = -0.5*cos(7*M_PI/36)};
  objects[60] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*sin(7*M_PI/36), .y = setposition, .vy = 0.5*cos(7*M_PI/36)};
  objects[61] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*cos(7*M_PI/36), .y = setposition, .vy = 0.5*sin(7*M_PI/36)};
  objects[62] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*cos(7*M_PI/36), .y = setposition, .vy = -0.5*sin(7*M_PI/36)};
  objects[63] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*cos(7*M_PI/36), .y = setposition, .vy = -0.5*sin(7*M_PI/36)};
  objects[64] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*cos(7*M_PI/36), .y = setposition, .vy = 0.5*sin(7*M_PI/36)};
  objects[65] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*sin(2*M_PI/9), .y = setposition, .vy = 0.5*cos(2*M_PI/9)};
  objects[66] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*sin(2*M_PI/9), .y = setposition, .vy = -0.5*cos(2*M_PI/9)};
  objects[67] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*sin(2*M_PI/9), .y = setposition, .vy = -0.5*cos(2*M_PI/9)};
  objects[68] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*sin(2*M_PI/9), .y = setposition, .vy = 0.5*cos(2*M_PI/9)};
  objects[69] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*cos(2*M_PI/9), .y = setposition, .vy = 0.5*sin(2*M_PI/9)};
  objects[70] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*cos(2*M_PI/9), .y = setposition, .vy = -0.5*sin(2*M_PI/9)};
  objects[71] = (Object){ .m = 60.0, .x = 0, .vx = 0.5*cos(2*M_PI/9), .y = setposition, .vy = -0.5*sin(2*M_PI/9)};
  objects[72] = (Object){ .m = 60.0, .x = 0, .vx = -0.5*cos(2*M_PI/9), .y = setposition, .vy = 0.5*sin(2*M_PI/9)};

  objects[73] = (Object){ .m = 60.0, .x = 0, .vx = -0.4, .y = setposition, .vy = 0.0};
  objects[74] = (Object){ .m = 60.0, .x = 0, .vx = 0.4, .y = setposition, .vy = 0.0};
  objects[75] = (Object){ .m = 60.0, .x = 0, .vx = 0.4/sqrt(2), .y = setposition, .vy = 0.4/sqrt(2)};
  objects[76] = (Object){ .m = 60.0, .x = 0, .vx = -0.4/sqrt(2), .y = setposition, .vy = 0.4/sqrt(2)};
  objects[77] = (Object){ .m = 60.0, .x = 0, .vx = 0.4/sqrt(2), .y = setposition, .vy = -0.4/sqrt(2)};
  objects[78] = (Object){ .m = 60.0, .x = 0, .vx = -0.4/sqrt(2), .y = setposition, .vy = -0.4/sqrt(2)};
  objects[79] = (Object){ .m = 60.0, .x = 0, .vx = 1/5, .y = setposition, .vy = sqrt(3)/5};
  objects[80] = (Object){ .m = 60.0, .x = 0, .vx = -1/5, .y = setposition, .vy = -sqrt(3)/5};
  objects[81] = (Object){ .m = 60.0, .x = 0, .vx = 1/5, .y = setposition, .vy = -sqrt(3)/5};
  objects[82] = (Object){ .m = 60.0, .x = 0, .vx = -1/5, .y = setposition, .vy = sqrt(3)/5};
  objects[83] = (Object){ .m = 60.0, .x = 0, .vx = sqrt(3)/5, .y = setposition, .vy = 1/5};
  objects[84] = (Object){ .m = 60.0, .x = 0, .vx = -sqrt(3)/5, .y = setposition, .vy = -1/5};
  objects[85] = (Object){ .m = 60.0, .x = 0, .vx = sqrt(3)/5,.y = setposition, .vy = -1/5};
  objects[86] = (Object){ .m = 60.0, .x = 0, .vx = -sqrt(3)/5, .y = setposition, .vy = 1/5};
  objects[87] = (Object){ .m = 60.0, .x = 0, .vx = (sqrt(6)-sqrt(2))/10, .y = setposition, .vy = (sqrt(6)+sqrt(2))/10};
  objects[88] = (Object){ .m = 60.0, .x = 0, .vx = -(sqrt(6)-sqrt(2))/10, .y = setposition, .vy = -(sqrt(6)+sqrt(2))/10};
  objects[89] = (Object){ .m = 60.0, .x = 0, .vx = (sqrt(6)-sqrt(2))/10, .y = setposition, .vy = -(sqrt(6)+sqrt(2))/10};
  objects[90] = (Object){ .m = 60.0, .x = 0, .vx = -(sqrt(6)-sqrt(2))/10, .y = setposition, .vy = (sqrt(6)+sqrt(2))/10};
  objects[91] = (Object){ .m = 60.0, .x = 0, .vx = (sqrt(6)+sqrt(2))/10, .y = setposition, .vy = (sqrt(6)-sqrt(2))/10};
  objects[92] = (Object){ .m = 60.0, .x = 0, .vx = -(sqrt(6)+sqrt(2))/10, .y = setposition, .vy = -(sqrt(6)-sqrt(2))/10};
  objects[93] = (Object){ .m = 60.0, .x = 0, .vx = (sqrt(6)+sqrt(2))/10, .y = setposition, .vy = -(sqrt(6)-sqrt(2))/10};
  objects[94] = (Object){ .m = 60.0, .x = 0, .vx = -(sqrt(6)+sqrt(2))/10, .y = setposition, .vy = (sqrt(6)-sqrt(2))/10};
  objects[95] = (Object){ .m = 60.0, .x = 0, .vx = 0, .y = setposition, .vy = 0.4};
  objects[96] = (Object){ .m = 60.0, .x = 0, .vx = 0, .y = setposition, .vy = 0.4};
  objects[97] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*sin(M_PI/36), .y = setposition, .vy = 0.4*cos(M_PI/36)};
  objects[98] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*sin(M_PI/36), .y = setposition, .vy = -0.4*cos(M_PI/36)};
  objects[99] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*sin(M_PI/36), .y = setposition, .vy = -0.4*cos(M_PI/36)};
  objects[100] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*sin(M_PI/36), .y = setposition, .vy = 0.4*cos(M_PI/36)};
  objects[101] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*cos(M_PI/36), .y = setposition, .vy = 0.4*sin(M_PI/36)};
  objects[102] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*cos(M_PI/36), .y = setposition, .vy = -0.4*sin(M_PI/36)};
  objects[103] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*cos(M_PI/36), .y = setposition, .vy = -0.4*sin(M_PI/36)};
  objects[104] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*cos(M_PI/36), .y = setposition, .vy = 0.4*sin(M_PI/36)};
  objects[105] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*cos(M_PI/18), .y = setposition, .vy = 0.4*sin(M_PI/18)};
  objects[106] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*cos(M_PI/18), .y = setposition, .vy = -0.4*sin(M_PI/18)};
  objects[107] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*cos(M_PI/18), .y = setposition, .vy = -0.4*sin(M_PI/18)};
  objects[108] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*cos(M_PI/18), .y = setposition, .vy = 0.4*sin(M_PI/18)};
  objects[109] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*sin(M_PI/18), .y = setposition, .vy = 0.4*cos(M_PI/18)};
  objects[110] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*sin(M_PI/18), .y = setposition, .vy = -0.4*cos(M_PI/18)};
  objects[111] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*sin(M_PI/18), .y = setposition, .vy = -0.4*cos(M_PI/18)};
  objects[112] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*sin(M_PI/18), .y = setposition, .vy = 0.4*cos(M_PI/18)};
  objects[113] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*sin(M_PI/9), .y = setposition, .vy = 0.4*cos(M_PI/9)};
  objects[114] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*sin(M_PI/9), .y = setposition, .vy = -0.4*cos(M_PI/9)};
  objects[115] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*sin(M_PI/9), .y = setposition, .vy = -0.4*cos(M_PI/9)};
  objects[116] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*sin(M_PI/9), .y = setposition, .vy = 0.4*cos(M_PI/9)};
  objects[117] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*cos(M_PI/9), .y = setposition, .vy = 0.4*sin(M_PI/9)};
  objects[118] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*cos(M_PI/9), .y = setposition, .vy = -0.4*sin(M_PI/9)};
  objects[119] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*cos(M_PI/9), .y = setposition, .vy = -0.4*sin(M_PI/9)};
  objects[120] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*cos(M_PI/9), .y = setposition, .vy = 0.4*sin(M_PI/9)};
  objects[121] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*sin(5*M_PI/36), .y = setposition, .vy = 0.4*cos(5*M_PI/36)};
  objects[122] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*sin(5*M_PI/36), .y = setposition, .vy = -0.4*cos(5*M_PI/36)};
  objects[123] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*sin(5*M_PI/36), .y = setposition, .vy = -0.4*cos(5*M_PI/36)};
  objects[124] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*sin(5*M_PI/36), .y = setposition, .vy = 0.4*cos(5*M_PI/36)};
  objects[125] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*cos(5*M_PI/36), .y = setposition, .vy = 0.4*sin(5*M_PI/36)};
  objects[126] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*cos(5*M_PI/36), .y = setposition, .vy = -0.4*sin(5*M_PI/36)};
  objects[127] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*cos(5*M_PI/36), .y = setposition, .vy = -0.4*sin(5*M_PI/36)};
  objects[128] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*cos(5*M_PI/36), .y = setposition, .vy = 0.4*sin(5*M_PI/36)};
  objects[129] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*sin(7*M_PI/36), .y = setposition, .vy = 0.4*cos(7*M_PI/36)};
  objects[130] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*sin(7*M_PI/36), .y = setposition, .vy = -0.4*cos(7*M_PI/36)};
  objects[131] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*sin(7*M_PI/36), .y = setposition, .vy = -0.4*cos(7*M_PI/36)};
  objects[132] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*sin(7*M_PI/36), .y = setposition, .vy = 0.4*cos(7*M_PI/36)};
  objects[133] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*cos(7*M_PI/36), .y = setposition, .vy = 0.4*sin(7*M_PI/36)};
  objects[134] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*cos(7*M_PI/36), .y = setposition, .vy = -0.4*sin(7*M_PI/36)};
  objects[135] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*cos(7*M_PI/36), .y = setposition, .vy = -0.4*sin(7*M_PI/36)};
  objects[136] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*cos(7*M_PI/36), .y = setposition, .vy = 0.4*sin(7*M_PI/36)};
  objects[137] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*sin(2*M_PI/9), .y = setposition, .vy = 0.4*cos(2*M_PI/9)};
  objects[138] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*sin(2*M_PI/9), .y = setposition, .vy = -0.4*cos(2*M_PI/9)};
  objects[139] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*sin(2*M_PI/9), .y = setposition, .vy = -0.4*cos(2*M_PI/9)};
  objects[140] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*sin(2*M_PI/9), .y = setposition, .vy = 0.4*cos(2*M_PI/9)};
  objects[141] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*cos(2*M_PI/9), .y = setposition, .vy = 0.4*sin(2*M_PI/9)};
  objects[142] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*cos(2*M_PI/9), .y = setposition, .vy = -0.4*sin(2*M_PI/9)};
  objects[143] = (Object){ .m = 60.0, .x = 0, .vx = 0.4*cos(2*M_PI/9), .y = setposition, .vy = -0.4*sin(2*M_PI/9)};
  objects[144] = (Object){ .m = 60.0, .x = 0, .vx = -0.4*cos(2*M_PI/9), .y = setposition, .vy = 0.4*sin(2*M_PI/9)};

  objects[145] = (Object){ .m = 60.0, .x = 0, .vx = -0.3, .y = setposition, .vy = 0.0};
  objects[146] = (Object){ .m = 60.0, .x = 0, .vx = 0.3, .y = setposition, .vy = 0.0};
  objects[147] = (Object){ .m = 60.0, .x = 0, .vx = 0.3/sqrt(2), .y = setposition, .vy = 0.3/sqrt(2)};
  objects[148] = (Object){ .m = 60.0, .x = 0, .vx = -0.3/sqrt(2), .y = setposition, .vy = 0.3/sqrt(2)};
  objects[149] = (Object){ .m = 60.0, .x = 0, .vx = 0.3/sqrt(2), .y = setposition, .vy = -0.3/sqrt(2)};
  objects[150] = (Object){ .m = 60.0, .x = 0, .vx = -0.3/sqrt(2), .y = setposition, .vy = -0.3/sqrt(2)};
  objects[151] = (Object){ .m = 60.0, .x = 0, .vx = 3/20, .y = setposition, .vy = 3*sqrt(3)/20};
  objects[152] = (Object){ .m = 60.0, .x = 0, .vx = -3/20, .y = setposition, .vy = -3*sqrt(3)/20};
  objects[153] = (Object){ .m = 60.0, .x = 0, .vx = 3/20, .y = setposition, .vy = -3*sqrt(3)/20};
  objects[154] = (Object){ .m = 60.0, .x = 0, .vx = -3/20, .y = setposition, .vy = 3*sqrt(3)/20};
  objects[155] = (Object){ .m = 60.0, .x = 0, .vx = 3*sqrt(3)/20, .y = setposition, .vy = 3/20};
  objects[156] = (Object){ .m = 60.0, .x = 0, .vx = -3*sqrt(3)/20, .y = setposition, .vy = -3/20};
  objects[157] = (Object){ .m = 60.0, .x = 0, .vx = 3*sqrt(3)/20, .y = setposition, .vy = -3/20};
  objects[158] = (Object){ .m = 60.0, .x = 0, .vx = -3*sqrt(3)/20, .y = setposition, .vy = 3/20};
  objects[159] = (Object){ .m = 60.0, .x = 0, .vx = 3*(sqrt(6)-sqrt(2))/40, .y = setposition, .vy = 3*(sqrt(6)+sqrt(2))/40};
  objects[160] = (Object){ .m = 60.0, .x = 0, .vx = -3*(sqrt(6)-sqrt(2))/40, .y = setposition, .vy = -3*(sqrt(6)+sqrt(2))/40};
  objects[161] = (Object){ .m = 60.0, .x = 0, .vx = 3*(sqrt(6)-sqrt(2))/40, .y = setposition, .vy = -3*(sqrt(6)+sqrt(2))/40};
  objects[162] = (Object){ .m = 60.0, .x = 0, .vx = -3*(sqrt(6)-sqrt(2))/40, .y = setposition, .vy = 3*(sqrt(6)+sqrt(2))/40};
  objects[163] = (Object){ .m = 60.0, .x = 0, .vx = 3*(sqrt(6)+sqrt(2))/40, .y = setposition, .vy = 3*(sqrt(6)-sqrt(2))/40};
  objects[164] = (Object){ .m = 60.0, .x = 0, .vx = -3*(sqrt(6)+sqrt(2))/40, .y = setposition, .vy = -3*(sqrt(6)-sqrt(2))/40};
  objects[165] = (Object){ .m = 60.0, .x = 0, .vx = 3*(sqrt(6)+sqrt(2))/40, .y = setposition, .vy = -3*(sqrt(6)-sqrt(2))/40};
  objects[166] = (Object){ .m = 60.0, .x = 0, .vx = -3*(sqrt(6)+sqrt(2))/40, .y = setposition, .vy = 3*(sqrt(6)-sqrt(2))/40};
  objects[167] = (Object){ .m = 60.0, .x = 0, .vx = 0, .y = setposition, .vy = 0.3};
  objects[168] = (Object){ .m = 60.0, .x = 0, .vx = 0, .y = setposition, .vy = 0.3};
  objects[169] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*sin(M_PI/36), .y = setposition, .vy = 0.3*cos(M_PI/36)};
  objects[170] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*sin(M_PI/36), .y = setposition, .vy = -0.3*cos(M_PI/36)};
  objects[171] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*sin(M_PI/36), .y = setposition, .vy = -0.3*cos(M_PI/36)};
  objects[172] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*sin(M_PI/36), .y = setposition, .vy = 0.3*cos(M_PI/36)};
  objects[173] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*cos(M_PI/36), .y = setposition, .vy = 0.3*sin(M_PI/36)};
  objects[174] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*cos(M_PI/36), .y = setposition, .vy = -0.3*sin(M_PI/36)};
  objects[175] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*cos(M_PI/36), .y = setposition, .vy = -0.3*sin(M_PI/36)};
  objects[176] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*cos(M_PI/36), .y = setposition, .vy = 0.3*sin(M_PI/36)};
  objects[177] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*cos(M_PI/18), .y = setposition, .vy = 0.3*sin(M_PI/18)};
  objects[178] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*cos(M_PI/18), .y = setposition, .vy = -0.3*sin(M_PI/18)};
  objects[179] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*cos(M_PI/18), .y = setposition, .vy = -0.3*sin(M_PI/18)};
  objects[180] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*cos(M_PI/18), .y = setposition, .vy = 0.3*sin(M_PI/18)};
  objects[181] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*sin(M_PI/18), .y = setposition, .vy = 0.3*cos(M_PI/18)};
  objects[182] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*sin(M_PI/18), .y = setposition, .vy = -0.3*cos(M_PI/18)};
  objects[183] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*sin(M_PI/18), .y = setposition, .vy = -0.3*cos(M_PI/18)};
  objects[184] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*sin(M_PI/18), .y = setposition, .vy = 0.3*cos(M_PI/18)};
  objects[185] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*sin(M_PI/9), .y = setposition, .vy = 0.3*cos(M_PI/9)};
  objects[186] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*sin(M_PI/9), .y = setposition, .vy = -0.3*cos(M_PI/9)};
  objects[187] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*sin(M_PI/9), .y = setposition, .vy = -0.3*cos(M_PI/9)};
  objects[188] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*sin(M_PI/9), .y = setposition, .vy = 0.3*cos(M_PI/9)};
  objects[189] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*cos(M_PI/9), .y = setposition, .vy = 0.3*sin(M_PI/9)};
  objects[190] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*cos(M_PI/9), .y = setposition, .vy = -0.3*sin(M_PI/9)};
  objects[191] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*cos(M_PI/9), .y = setposition, .vy = -0.3*sin(M_PI/9)};
  objects[192] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*cos(M_PI/9), .y = setposition, .vy = 0.3*sin(M_PI/9)};
  objects[193] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*sin(5*M_PI/36), .y = setposition, .vy = 0.3*cos(5*M_PI/36)};
  objects[194] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*sin(5*M_PI/36), .y = setposition, .vy = -0.3*cos(5*M_PI/36)};
  objects[195] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*sin(5*M_PI/36), .y = setposition, .vy = -0.3*cos(5*M_PI/36)};
  objects[196] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*sin(5*M_PI/36), .y = setposition, .vy = 0.3*cos(5*M_PI/36)};
  objects[197] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*cos(5*M_PI/36), .y = setposition, .vy = 0.3*sin(5*M_PI/36)};
  objects[198] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*cos(5*M_PI/36), .y = setposition, .vy = -0.3*sin(5*M_PI/36)};
  objects[199] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*cos(5*M_PI/36), .y = setposition, .vy = -0.3*sin(5*M_PI/36)};
  objects[200] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*cos(5*M_PI/36), .y = setposition, .vy = 0.3*sin(5*M_PI/36)};
  objects[201] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*sin(7*M_PI/36), .y = setposition, .vy = 0.3*cos(7*M_PI/36)};
  objects[202] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*sin(7*M_PI/36), .y = setposition, .vy = -0.3*cos(7*M_PI/36)};
  objects[203] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*sin(7*M_PI/36), .y = setposition, .vy = -0.3*cos(7*M_PI/36)};
  objects[204] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*sin(7*M_PI/36), .y = setposition, .vy = 0.3*cos(7*M_PI/36)};
  objects[205] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*cos(7*M_PI/36), .y = setposition, .vy = 0.3*sin(7*M_PI/36)};
  objects[206] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*cos(7*M_PI/36), .y = setposition, .vy = -0.3*sin(7*M_PI/36)};
  objects[207] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*cos(7*M_PI/36), .y = setposition, .vy = -0.3*sin(7*M_PI/36)};
  objects[208] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*cos(7*M_PI/36), .y = setposition, .vy = 0.3*sin(7*M_PI/36)};
  objects[209] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*sin(2*M_PI/9), .y = setposition, .vy = 0.3*cos(2*M_PI/9)};
  objects[210] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*sin(2*M_PI/9), .y = setposition, .vy = -0.3*cos(2*M_PI/9)};
  objects[211] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*sin(2*M_PI/9), .y = setposition, .vy = -0.3*cos(2*M_PI/9)};
  objects[212] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*sin(2*M_PI/9), .y = setposition, .vy = 0.3*cos(2*M_PI/9)};
  objects[213] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*cos(2*M_PI/9), .y = setposition, .vy = 0.3*sin(2*M_PI/9)};
  objects[214] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*cos(2*M_PI/9), .y = setposition, .vy = -0.3*sin(2*M_PI/9)};
  objects[215] = (Object){ .m = 60.0, .x = 0, .vx = 0.3*cos(2*M_PI/9), .y = setposition, .vy = -0.3*sin(2*M_PI/9)};
  objects[216] = (Object){ .m = 60.0, .x = 0, .vx = -0.3*cos(2*M_PI/9), .y = setposition, .vy = 0.3*sin(2*M_PI/9)};

  objects[217] = (Object){ .m = 60.0, .x = 0, .vx = -0.2, .y = setposition, .vy = 0.0};
  objects[218] = (Object){ .m = 60.0, .x = 0, .vx = 0.2, .y = setposition, .vy = 0.0};
  objects[219] = (Object){ .m = 60.0, .x = 0, .vx = 0.2/sqrt(2), .y = setposition, .vy = 0.2/sqrt(2)};
  objects[220] = (Object){ .m = 60.0, .x = 0, .vx = -0.2/sqrt(2), .y = setposition, .vy = 0.2/sqrt(2)};
  objects[221] = (Object){ .m = 60.0, .x = 0, .vx = 0.2/sqrt(2), .y = setposition, .vy = -0.2/sqrt(2)};
  objects[222] = (Object){ .m = 60.0, .x = 0, .vx = -0.2/sqrt(2), .y = setposition, .vy = -0.2/sqrt(2)};
  objects[223] = (Object){ .m = 60.0, .x = 0, .vx = 1/10, .y = setposition, .vy = sqrt(3)/10};
  objects[224] = (Object){ .m = 60.0, .x = 0, .vx = -1/10, .y = setposition, .vy = -sqrt(3)/10};
  objects[225] = (Object){ .m = 60.0, .x = 0, .vx = 1/10, .y = setposition, .vy = -sqrt(3)/10};
  objects[226] = (Object){ .m = 60.0, .x = 0, .vx = -1/10, .y = setposition, .vy = sqrt(3)/10};
  objects[227] = (Object){ .m = 60.0, .x = 0, .vx = sqrt(3)/10, .y = setposition, .vy = 1/10};
  objects[228] = (Object){ .m = 60.0, .x = 0, .vx = -sqrt(3)/10, .y = setposition, .vy = -1/10};
  objects[229] = (Object){ .m = 60.0, .x = 0, .vx = sqrt(3)/10,.y = setposition, .vy = -1/10};
  objects[230] = (Object){ .m = 60.0, .x = 0, .vx = -sqrt(3)/10, .y = setposition, .vy = 1/10};
  objects[231] = (Object){ .m = 60.0, .x = 0, .vx = (sqrt(6)-sqrt(2))/20, .y = setposition, .vy = (sqrt(6)+sqrt(2))/20};
  objects[232] = (Object){ .m = 60.0, .x = 0, .vx = -(sqrt(6)-sqrt(2))/20, .y = setposition, .vy = -(sqrt(6)+sqrt(2))/20};
  objects[233] = (Object){ .m = 60.0, .x = 0, .vx = (sqrt(6)-sqrt(2))/20, .y = setposition, .vy = -(sqrt(6)+sqrt(2))/20};
  objects[234] = (Object){ .m = 60.0, .x = 0, .vx = -(sqrt(6)-sqrt(2))/20, .y = setposition, .vy = (sqrt(6)+sqrt(2))/20};
  objects[235] = (Object){ .m = 60.0, .x = 0, .vx = (sqrt(6)+sqrt(2))/20, .y = setposition, .vy = (sqrt(6)-sqrt(2))/20};
  objects[236] = (Object){ .m = 60.0, .x = 0, .vx = -(sqrt(6)+sqrt(2))/20, .y = setposition, .vy = -(sqrt(6)-sqrt(2))/20};
  objects[237] = (Object){ .m = 60.0, .x = 0, .vx = (sqrt(6)+sqrt(2))/20, .y = setposition, .vy = -(sqrt(6)-sqrt(2))/20};
  objects[238] = (Object){ .m = 60.0, .x = 0, .vx = -(sqrt(6)+sqrt(2))/20, .y = setposition, .vy = (sqrt(6)-sqrt(2))/20};
  objects[239] = (Object){ .m = 60.0, .x = 0, .vx = 0, .y = setposition, .vy = 0.2};
  objects[240] = (Object){ .m = 60.0, .x = 0, .vx = 0, .y = setposition, .vy = 0.2};
  objects[241] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*sin(M_PI/36), .y = setposition, .vy = 0.2*cos(M_PI/36)};
  objects[242] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*sin(M_PI/36), .y = setposition, .vy = -0.2*cos(M_PI/36)};
  objects[243] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*sin(M_PI/36), .y = setposition, .vy = -0.2*cos(M_PI/36)};
  objects[244] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*sin(M_PI/36), .y = setposition, .vy = 0.2*cos(M_PI/36)};
  objects[245] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*cos(M_PI/36), .y = setposition, .vy = 0.2*sin(M_PI/36)};
  objects[246] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*cos(M_PI/36), .y = setposition, .vy = -0.2*sin(M_PI/36)};
  objects[247] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*cos(M_PI/36), .y = setposition, .vy = -0.2*sin(M_PI/36)};
  objects[248] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*cos(M_PI/36), .y = setposition, .vy = 0.2*sin(M_PI/36)};
  objects[249] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*cos(M_PI/18), .y = setposition, .vy = 0.2*sin(M_PI/18)};
  objects[250] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*cos(M_PI/18), .y = setposition, .vy = -0.2*sin(M_PI/18)};
  objects[251] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*cos(M_PI/18), .y = setposition, .vy = -0.2*sin(M_PI/18)};
  objects[252] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*cos(M_PI/18), .y = setposition, .vy = 0.2*sin(M_PI/18)};
  objects[253] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*sin(M_PI/18), .y = setposition, .vy = 0.2*cos(M_PI/18)};
  objects[254] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*sin(M_PI/18), .y = setposition, .vy = -0.2*cos(M_PI/18)};
  objects[255] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*sin(M_PI/18), .y = setposition, .vy = -0.2*cos(M_PI/18)};
  objects[256] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*sin(M_PI/18), .y = setposition, .vy = 0.2*cos(M_PI/18)};
  objects[257] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*sin(M_PI/9), .y = setposition, .vy = 0.2*cos(M_PI/9)};
  objects[258] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*sin(M_PI/9), .y = setposition, .vy = -0.2*cos(M_PI/9)};
  objects[259] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*sin(M_PI/9), .y = setposition, .vy = -0.2*cos(M_PI/9)};
  objects[260] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*sin(M_PI/9), .y = setposition, .vy = 0.2*cos(M_PI/9)};
  objects[261] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*cos(M_PI/9), .y = setposition, .vy = 0.2*sin(M_PI/9)};
  objects[262] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*cos(M_PI/9), .y = setposition, .vy = -0.2*sin(M_PI/9)};
  objects[263] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*cos(M_PI/9), .y = setposition, .vy = -0.2*sin(M_PI/9)};
  objects[264] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*cos(M_PI/9), .y = setposition, .vy = 0.2*sin(M_PI/9)};
  objects[265] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*sin(5*M_PI/36), .y = setposition, .vy = 0.2*cos(5*M_PI/36)};
  objects[266] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*sin(5*M_PI/36), .y = setposition, .vy = -0.2*cos(5*M_PI/36)};
  objects[267] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*sin(5*M_PI/36), .y = setposition, .vy = -0.2*cos(5*M_PI/36)};
  objects[268] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*sin(5*M_PI/36), .y = setposition, .vy = 0.2*cos(5*M_PI/36)};
  objects[269] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*cos(5*M_PI/36), .y = setposition, .vy = 0.2*sin(5*M_PI/36)};
  objects[270] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*cos(5*M_PI/36), .y = setposition, .vy = -0.2*sin(5*M_PI/36)};
  objects[271] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*cos(5*M_PI/36), .y = setposition, .vy = -0.2*sin(5*M_PI/36)};
  objects[272] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*cos(5*M_PI/36), .y = setposition, .vy = 0.2*sin(5*M_PI/36)};
  objects[273] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*sin(7*M_PI/36), .y = setposition, .vy = 0.2*cos(7*M_PI/36)};
  objects[274] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*sin(7*M_PI/36), .y = setposition, .vy = -0.2*cos(7*M_PI/36)};
  objects[275] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*sin(7*M_PI/36), .y = setposition, .vy = -0.2*cos(7*M_PI/36)};
  objects[276] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*sin(7*M_PI/36), .y = setposition, .vy = 0.2*cos(7*M_PI/36)};
  objects[277] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*cos(7*M_PI/36), .y = setposition, .vy = 0.2*sin(7*M_PI/36)};
  objects[278] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*cos(7*M_PI/36), .y = setposition, .vy = -0.2*sin(7*M_PI/36)};
  objects[279] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*cos(7*M_PI/36), .y = setposition, .vy = -0.2*sin(7*M_PI/36)};
  objects[280] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*cos(7*M_PI/36), .y = setposition, .vy = 0.2*sin(7*M_PI/36)};
  objects[281] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*sin(2*M_PI/9), .y = setposition, .vy = 0.2*cos(2*M_PI/9)};
  objects[282] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*sin(2*M_PI/9), .y = setposition, .vy = -0.2*cos(2*M_PI/9)};
  objects[283] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*sin(2*M_PI/9), .y = setposition, .vy = -0.2*cos(2*M_PI/9)};
  objects[284] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*sin(2*M_PI/9), .y = setposition, .vy = 0.2*cos(2*M_PI/9)};
  objects[285] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*cos(2*M_PI/9), .y = setposition, .vy = 0.2*sin(2*M_PI/9)};
  objects[286] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*cos(2*M_PI/9), .y = setposition, .vy = -0.2*sin(2*M_PI/9)};
  objects[287] = (Object){ .m = 60.0, .x = 0, .vx = 0.2*cos(2*M_PI/9), .y = setposition, .vy = -0.2*sin(2*M_PI/9)};
  objects[288] = (Object){ .m = 60.0, .x = 0, .vx = -0.2*cos(2*M_PI/9), .y = setposition, .vy = 0.2*sin(2*M_PI/9)};

  objects[289] = (Object){ .m = 60.0, .x = 0, .vx = -0.1, .y = setposition, .vy = 0.0};
  objects[290] = (Object){ .m = 60.0, .x = 0, .vx = 0.1, .y = setposition, .vy = 0.0};
  objects[291] = (Object){ .m = 60.0, .x = 0, .vx = 0.1/sqrt(2), .y = setposition, .vy = 0.1/sqrt(2)};
  objects[292] = (Object){ .m = 60.0, .x = 0, .vx = -0.1/sqrt(2), .y = setposition, .vy = 0.1/sqrt(2)};
  objects[293] = (Object){ .m = 60.0, .x = 0, .vx = 0.1/sqrt(2), .y = setposition, .vy = -0.1/sqrt(2)};
  objects[294] = (Object){ .m = 60.0, .x = 0, .vx = -0.1/sqrt(2), .y = setposition, .vy = -0.1/sqrt(2)};
  objects[295] = (Object){ .m = 60.0, .x = 0, .vx = 1/20, .y = setposition, .vy = sqrt(3)/20};
  objects[296] = (Object){ .m = 60.0, .x = 0, .vx = -1/20, .y = setposition, .vy = -sqrt(3)/20};
  objects[297] = (Object){ .m = 60.0, .x = 0, .vx = 1/20, .y = setposition, .vy = -sqrt(3)/20};
  objects[298] = (Object){ .m = 60.0, .x = 0, .vx = -1/20, .y = setposition, .vy = sqrt(3)/20};
  objects[299] = (Object){ .m = 60.0, .x = 0, .vx = sqrt(3)/4, .y = setposition, .vy = 1/20};
  objects[300] = (Object){ .m = 60.0, .x = 0, .vx = -sqrt(3)/4, .y = setposition, .vy = -1/20};
  objects[301] = (Object){ .m = 60.0, .x = 0, .vx = sqrt(3)/4, .y = setposition, .vy = -1/20};
  objects[302] = (Object){ .m = 60.0, .x = 0, .vx = -sqrt(3)/4, .y = setposition, .vy = 1/20};
  objects[303] = (Object){ .m = 60.0, .x = 0, .vx = (sqrt(6)-sqrt(2))/40, .y = setposition, .vy = (sqrt(6)+sqrt(2))/40};
  objects[304] = (Object){ .m = 60.0, .x = 0, .vx = -(sqrt(6)-sqrt(2))/40, .y = setposition, .vy = -(sqrt(6)+sqrt(2))/40};
  objects[305] = (Object){ .m = 60.0, .x = 0, .vx = (sqrt(6)-sqrt(2))/40, .y = setposition, .vy = -(sqrt(6)+sqrt(2))/40};
  objects[306] = (Object){ .m = 60.0, .x = 0, .vx = -(sqrt(6)-sqrt(2))/40, .y = setposition, .vy = (sqrt(6)+sqrt(2))/40};
  objects[307] = (Object){ .m = 60.0, .x = 0, .vx = (sqrt(6)+sqrt(2))/40, .y = setposition, .vy = (sqrt(6)-sqrt(2))/40};
  objects[308] = (Object){ .m = 60.0, .x = 0, .vx = -(sqrt(6)+sqrt(2))/40, .y = setposition, .vy = -(sqrt(6)-sqrt(2))/40};
  objects[309] = (Object){ .m = 60.0, .x = 0, .vx = (sqrt(6)+sqrt(2))/40, .y = setposition, .vy = -(sqrt(6)-sqrt(2))/40};
  objects[310] = (Object){ .m = 60.0, .x = 0, .vx = -(sqrt(6)+sqrt(2))/40, .y = setposition, .vy = (sqrt(6)-sqrt(2))/40};
  objects[311] = (Object){ .m = 60.0, .x = 0, .vx = 0, .y = setposition, .vy = 0.5};
  objects[312] = (Object){ .m = 60.0, .x = 0, .vx = 0, .y = setposition, .vy = 0.5};


  while (t<400){
    t = i * cond.dt;
    i++;
    
    my_update_velocities(objects, objnum, cond);
    my_update_positions(objects, objnum, cond);
    
    // 表示の座標系は width/2, height/2 のピクセル位置が原点となるようにする
    my_plot_objects(objects, objnum, t, cond);
    
    usleep(200 * 1000); // 200 x 1000us = 200 ms ずつ停止
    printf("\e[%dA", cond.height+2);// 壁とパラメータ表示分で2行
  }
  return EXIT_SUCCESS;
}

// 実習: 以下に my_ で始まる関数を実装する
// 最終的に phisics2.h 内の事前に用意された関数プロトタイプをコメントアウト
