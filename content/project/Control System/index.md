---
title: Global Path Planning and Local Path Planning for Mobile Robots
summary: Implement using Algorithm A and Algorithm B respectively and simulate using MATLAB
tags:
  - Control System
date: '2023-11-09T00:00:00Z'

# Optional external URL for project (replaces project detail page).
external_link: ''

image:
  caption: Photo by Zhang Tong on Unsplash
  focal_point: Smart

links:
  - icon: envelope
    icon_pack: fas
    name: Contact
    url: mailto:ztongkop@foxmail.com
url_code: ''
url_pdf: ''
url_slides: ''
url_video: ''

# Slides (optional).
#   Associate this project with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
slides: 
---
1 Global Path Planning

The **A * algorithm** is one of the most effective algorithms for global path planning methods in static environments. It not only considers the shortest distance from the extension point to the starting point, but also takes the shortest distance from the target point as a reference, which can not only achieve the shortest path, but also ensure the inspiration of the path points to avoid blind point selection. When there are few obstacles, fast planning can be carried out by changing the search neighborhood range, which can speed up the approximation to the target point. Its evaluation function is:


$$
\sum_{i=0}^n i^2 = \frac{(n^2+n)(2n+1)}{6}
$$


$F(n)=G(n)+F(n)$

Where F (n) is the estimated value of the sum of the distance from node n to the starting point and the distance to the target point; G (n) is the estimated value of the distance from node n to the starting point; H (n) is the estimated value of the distance from node n to the target node. The point with the smallest estimated value F (n) is used as an alternative path node.

The workspace of the mobile robot is divided into two-dimensional raster map, which should contain the starting point, target point and surrounding obstacles information of the mobile robot. Usually, the starting point and the target point occupy only one grid, and the mobile robot is regarded as a particle point. The mobile robot searches for eight adjacent grids at a time, and selects the grid with the smallest cost value as an alternative extension point by calculating the cost estimate of the adjacent eight grids.

First, the following assumptions are made: 

1. The coordinates of the scalable node in the grid map are (xi, yi); 
2. The position of the target point is (xend, yend); 
3. H (xi, yi) is the estimated value of the heuristic distance, from which the distance between the two points can be estimated as:	

Euclidean distance: The linear distance estimation method between two points is defined as

$H(x_i,y_i)=\sqrt{(x_{\mathrm{end}}-x_i)^2+(y_{end}-y_i)^2}$

Manhattan distance: The Manhattan distance is composed of the sum of two distances, namely the horizontal distance and the vertical distance. Its expression can be defined as:

H\left(x_i,y_i\right)=abs\left(x_{\mathrm{end\ }}-x_i\right)+abs\left(y_{\mathrm{end\ }}-y_i\right)

Each square in the figure is marked with the values of F, G, and H. The upper left corner is F, the lower left corner is G, and the lower right corner is H, where the green point is the starting point, the red point is the target point, the blue grid is the obstacle point, and the blue border grid forms a path from the starting point to the target point.

The A * algorithm usually creates two tables in the extended search, namely the open list and the closed list, hereinafter referred to as the "O table" and the "C table" respectively. The trajectory of the optimal path planned in the future is the trajectory composed of the points stored in the O table. The expansion steps are:

1.After completing the division of the area, set the starting point to the current point, that is, (S) into the O table, and clear the C table to zero;

2.Put the expandable points n around S (the grid occupied by the obstacle is a non-expandable point) into the O table, and the starting point S is the parent node;

3.Retrieve whether there is an expandable point in the O list, if not, the pathfinding fails; if there is, continue the search;

4.Put the current point into table C, and calculate the cost value F of the scalable point n in table O through the cost function, and set the point with the smallest F value as the current point, and at the same time as the parent node, put other scalable points into table C;

5.Put the expandable nodes around the current point into the O table, and determine whether the current point is the target node. If so, end the entry.
All the parent nodes in the algorithm process are traced back, which is the shortest path.

The flow chart of the A * algorithm can be expressed as follows:


The experimental program design pseudocode based on the A * algorithm is as follows:
create a data structure Node:

structure Node{
   double g;    
   double h;   
   double f;    
   Node*prefore; // prefore node of the node}
A Star(){
   structure Node start_node;
   start_node.g = 0;
   start_node.h = H(start);
   start_node.f = start_node.h;
   start_node.prefore =为空;
   O = [start_node]; C = [];
   while ( O 表非空) {
      Calculate O list node’s smallest f;
      which is called x_node, and the corresponding node is called x;
      Remove x_node from the O list;
      if (x is target){
          Return path;
     according to the prefore pointer of the node structure corresponding to each node; }
      for (each successor node y of x){
          structure  Node  y_node;
          y_node.g = x_node.g+w(x,y);
          y_node.h = H(y);
          y_node.f = y_node.g+y_node.h;
          y.prefore = x_node;
          if(y is not in the O table and not in the C table){
             Put y_node in the O table;
          }else if(y is in the O table){
             Take out the Node structure corresponding to the y node in the O table,
             which is called y_open;
             if( y_node.f < y_open.f) {
                y_open.g = y_node.g;
                y_open.h = y_node.h;
                y_open.f = y_node.f;
                y_open.prefore = y_node.prefore;
             }
          }else{Take out the Node structure corresponding to the y node in the C table,
             which is called y_close;
             if(y_node.f < y_close.f){ 
                Remove y_close from the C list
                Put y_node in the O table; }
        }
      }  
      Put x_node into the C table;}  }  

MATLAB R2016a is used to experiment with the algorithm. The size of the environment map is 40 * 80 rectangular grid map as shown in the figure. The construction of the environment simulates the warehousing and logistics scene, which includes six rows of neatly arranged shelves. The obstacles marked at the four corners are simulated building structures. Other random obstacles that may exist in this paper are not set, such as other robots at work.


From the graph, two methods of distance prediction function can be known: (a) A * algorithm path based on Euclidean distance prediction to target point distance estimate; (b) A * path graph based on Manhattan distance prediction to target point distance estimate.

 2 Local Path Planning

The Dynamic Window Approach (DWA) algorithm restricts the traditional mobile robot to the method of using position control to achieve obstacle avoidance, and converts the cohesion into the method of using the speed prediction control of the mobile robot in the next cycle to achieve obstacle avoidance. Therefore, the window of the dynamic window is the speed window [8]. The dynamic window refers to simulating a series of speeds of the mobile robot's forward direction at the next moment to form a two-dimensional speed space (including linear velocity and angular velocity). Predicting the path of the alternative speed space eliminates the speed that may collide with the obstacles in the environment, and then selects an optimal speed from the feasible speed space as the speed of the next motion cycle, as shown in the figure.

(1) Kinematic modeling of mobile robots based on DWA algorithm
The predicted trajectory set of the mobile robot can be used to form a series of arcs and curves in the forward direction of the robot. As shown in Figure 4.5, because the mobile robot can move a limited distance in one cycle, its trajectory can be approximated as a straight line. That is, in the robot coordinate system, the distance of the axis movement is V_t\ times\ mathrm {\ Delta t}, and the projected distance in the x-axis and y-axis directions of the world coordinate system is calculated respectively. That is, the distance delta x and delta y traveled by the mobile robot in the world coordinate system in one delta t cycle can be obtained, which are expressed as:


