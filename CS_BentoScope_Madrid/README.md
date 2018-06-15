# CS_BentoScope_Madrid
BentoScope is a mini version of CityScope urban decision support system. CS_BentoScope_Madrid was used in the 2018 June "Beyond Smart City" workshop in Madrid, collabrating with Norman Foster Fundation. It provides two scales of urban decision support. 

Algorithms:

1 Solar access (showing the solar gain of a building roof):
    inquiry 16 by 16 grid of cells (no filter)
    for each cell, define it as (bx,by) and create a solar_index with 0 value
    make 3-row list of influenced cells (nx,ny) that can cover the cell:
    row1                         (bx-1,by+1),(bx,by+1),(bx+1,by+1)
    row2             (bx-2,by+2),(bx-1,by+2),(bx,by+2),(bx+1,by+2),(bx+2,by+2)
    row3 (bx-3,by+3),(bx-2,by+3),(bx-1,by+3),(bx,by+3),(bx+1,by+3),(bx+2,by+3),(bx+3,by+3)

    and their coefficients because of their solar angles toward (bx,by):
    row1                  0.5,1,0.5
    row2             0.25,0.5,1,0.5,0.25
    row3       0.125,0.25,0.5,1,0.5,0.25,0.125

    since maximum floor number is 30 and its height is 30*3.5m, we will consider from row 1 to row int(30*3.5/cell_size)
    for each influenced cell, solar_index += (height(nx,ny)-height(bx,by))*coefficient
    
    regularize the solar_index to 0~1 based which 0 is the worst and 1 is the best (with 100% solar access) by the formula: max(1-solar_index/100,0)

    Solar radar chart score: the average of all cells' regularized solar_index

2 Traffic (showing the network access of road cells):
    inquiry 16 by 16 grid of cells (only road ids: 6)
    for each road cell, define it as (bx,by) and create a population with 0 value
    add the residents and employees in its four neighbors (up, down, left, right) and we can get the population that influxing into this cell
    
    inquiry 16 by 16 grid of cells (only road ids: 6)
    for each road cell, define it as (cx,cy) and create a traffic_index = its own population value
    add its four neighbors' 50% population into the traffic_index
    add its four 2-cell-distance neighbors' 50% population into the traffic_index

    regularize the traffic_index to 0~1 based which 0 is the worst and 1 is the best (with 100% fluent traffic) by the formula: max(0,1-traffic_index/30000 * (1-balance)) when balance is from live-work balance index and representing how many percentages of people will not be inclined to private vehicles.

    Traffic radar chart score: the average of all cells' regularized traffic_index

3 live-work balance (showing the residents and employee positions nearby a building):
    inquiry 16 by 16 grid of cells (only building ids: 0,1,2,3,4,5)
    for each cell, define it as (bx,by) and create a population with 0 value
    add the residents and employees in its 7 by 7 neighbors and accumulate their residents and employees separately

    regularize the balance_index to 0~1 based which 0 is the worst and 1 is the best (with 100% residents = employees in 7 by 7 grid) by the formula: 1-max(0.5-res/(res+emp),res/(res+emp)-0.5)*2

    Live-work radar chart score: the average of all cells' regularized balance_index

4 Working proximity (average distance to the closest three office cells)：
    inquiry 16 by 16 grid of cells (only residential building ids: 0,1,2) into group res
    inquiry 16 by 16 grid of cells (only office building ids: 0,1,2) into group off
    for each res cell, measure the three closest points in off and get the average distance
    set coefficient as 300 meters
    since the smallest distance is (1+sqrt(2)+sqrt(2))/3 * cell_size = 1.276 * cell_size, regularize the work_prox to 0~1 based which 0 is the worst and 1 is the best (within 1.276 * cell_size average distance) by the formula: max(0,1-(average_distance-1.276 * cell_size)/coefficient)

    Working proximity radar chart score: the average of all cells' regularized work_prox

5 Living proximity (average distance to the closest three residential cells)：
    inquiry 16 by 16 grid of cells (only residential building ids: 0,1,2) into group res
    inquiry 16 by 16 grid of cells (only office building ids: 0,1,2) into group off
    for each off cell, measure the three closest points in res and get the average distance
    set coefficient as 300 meters
    since the smallest distance is (1+sqrt(2)+sqrt(2))/3 * cell_size = 1.276 * cell_size, regularize the live_prox to 0~1 based which 0 is the worst and 1 is the best (within 1.276 * cell_size average distance) by the formula: max(0,1-(average_distance-1.276 * cell_size)/coefficient)

    Living proximity radar chart score: the average of all cells' regularized live_prox

6 Park proximity (average distance to the closest park cells)：
    inquiry 16 by 16 grid of cells (only building ids: 0,1,2,3,4,5) into group bldg
    inquiry 16 by 16 grid of cells (only park ids: 7) into group park
    for each bldg cell, measure the closest points in park and get the distance
    set coefficient as 300 meters
    since the smallest distance is 1 cell_size, regularize the park_prox to 0~1 based which 0 is the worst and 1 is the best (within 1*cell_size distance) by the formula: max(0,1-(distance-cell_size)/coefficient)

    Park proximity radar chart score: the average of all cells' regularized park_prox