# CS_BentoScope_Madrid
BentoScope is a mini version of CityScope urban decision support system. CS_BentoScope_Madrid was used in the 2018 June "Beyond Smart City" workshop in Madrid, collaborating with Norman Foster Foundation. It provides two scales of urban decision support. 

Algorithms:

1.	Population Density:
    •	For each cell, calculate the population density (including residents and employees) within 200 meters radius’ circle.
    •	Radar Chart: the average score of all cells

2.	Population Diversity:
    •	For each cell, calculate the population sharing percentages of 7 types of buildings (including three housings and three offices and one industry) divided by total population within 200 meters radius’ circle. 
    •	use the formula sum(Pi*log10(Pi))/-log10(7), 0<=i<=7 to get the diversity score for each cell.
    •	Radar Chart: the average score of all cells

3.	Energy Efficiency:
    •	For each cell, get the sum of (Nearest N Building Population/Distance to that building), influenced by building types: (RL:-0.2, RM:0.0, RS:0.2, OL:-0.4, OM:0.0, OS:0.4, CI:1.0)
    •	Radar Chart: the average score of all cells

4.	Working proximity:
    •	For each residential cell, get the average distance to the closest three office/industrial cells
    •	normalize by the walkable distance that set by users.
    •	Radar Chart: the average score of all cells

5.	Living proximity:
    •	For each working cell, get the average distance to the closest three residential cells
    •	Normalize the score with the walkable distance that set by users.
    •	Radar Chart: the average score of all cells

6.	Park proximity:
    •	For each building cell, get the average distance to the closest park cell 
    •	normalize by the walkable distance that set by users.
    •	Radar Chart: the average score of all cells

7.	Live-work balance:
    •	For each building cell, get the percentage of residents out of total population nearby this building in seven by seven grid
    •	normalize the percentage by the difference toward 0.5
    •	Radar Chart: the average score of all cells

8.	Solar access:
    •	showing the solar gain of a cell
    •	For each cell, get the sum of building shading (3 rows) in its south
    •	normalize by 100 into 0 to 1 range which 0 is the worst and 1 is the best (with 100% solar access)
    •	Radar Chart: the average score of all cells

9.	Mobility Performance:
    •	showing the network accessibility of road cells
    •	For each road cell (6, 9), get the sum of the population in its four neighbors (up, down, left, right) (means the people access the road from buildings)
    •	After calculating the direct access population, add each road’s four neighbors' 50% population, representing the movements.
    •	Normalize to 0~1 based which 0 is the worst and 1 is the best (with 100% good traffic) by the formula: max(0,1-traffic_index/30000 * (1-balance)) when the balance is from live-work balance score and representing how many percentages of people won’t use private traffic.
    •	Radar Chart: the average score of all cells
