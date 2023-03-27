---
layout: page
title: approximate NMPC for flexible robots
description: controlling flexible robots fast using safe approximate NMPC
img: assets/img/flexible_robot.png
importance: 2
category: work
---

# Intro
Robot manipulators have a long history; it all started in the middle of the 20th century. First robots that entered the market were rigid link and rigid joint robots and they are still widely used, for example, in car manufactuting industry. Then, around 90's [DLR]() started a research on flexible joint robots that later became KUKA IIWA. Around the same time there was an extensive research on flexible link robots, that, unfortunately, didn't make it to industry. Nowdays, there is a lot of hype around soft robots. It is difficult to predict the future, maybe one day soft robots will become widespread and used all over the place. However, I think flexible robots are the necessary evil on the way from rigid link, flexible joint robots to soft robots. 

Why do we need the transition? There are two main reasons: 
 - current collaborative robots are not strong -- the load-to-mass ratio is less than 1
 - they are not made for truly safe human-robot collaboration, there is no mechanical compliance that can buffer the impact during collision



<div class="row mt-3">
    <div class="col-sm-10 mx-auto">
        {% include figure.html path="assets/img/blog/manipulator_evolution.png" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    A simple, elegant caption looks good between image rows, after each row, or doesn't have to be there at all.
</div>