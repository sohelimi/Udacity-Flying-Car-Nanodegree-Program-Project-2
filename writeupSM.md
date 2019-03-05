
## Project: 3D Motion Planning

"""
FCND
Project-2
SM Sohelozzaman
May 2 25, 2018
"""

## Rubric Points
### Here I will consider the [rubric](https://review.udacity.com/#!/rubrics/1534/view) 
Important points individually describe how I addressed each point in my implementation.

---

### Explain the Starter Code

#### 1. Explain the functionality of what's provided in `motion_planning.py` and `planning_utils.py`
The motion_planning.py and planning_utils.py scripts contain a basic planning implementation that includes a state based control loop for managing the drones behaviour similar
to the backyard_flyer_solution.py.  The functionality in `planning_utils.py` allows the world to be discretised into a grid or graph representation, 
while the `motion_planning.py` code is an implementation of the Drone object for controlling the behaviour and mission of the drone.

An overview of the two solutions provided are as follows:

Feature | backyard_flyer | motion_planning
--- | --- | ---
`Implements a path planner for finding paths` | no | **yes**
`Uses dynamic searching for missions` | no | **yes**
`Includes an internal state of the configuration space` | no | **yes**
`Uses Global Positioning for following paths` | no | **yes**

### Implementing Your Path Planning Algorithm

#### 1. Set your global home position
To accurately generate a valid path that the quadcopter can follow, it needs to know the relative coordinates of each successive waypoint.  
This is accomplished by setting the home location, which in this case is obtained by reading the provided configuration space state in the 'colliders' file.

``` 
    lat0 = float(home_pos_data[0].strip().split(" ")[1])
    lon0 = float(home_pos_data[1].strip().split(" ")[1])
    
    self.set_home_position(lon0, lat0, 0)
    global_pos = [self._longitude, self._latitude, self._altitude]
```
*From the above code, home position is set from the provided environment file.*

#### 2. Set your current local position
After the global coordinates have been obtained, the quadcopter needs to realise the home location for advancing to waypoints relative to its position in the world.  
This is achieved by the following line:

```
     local_pos = global_to_local(global_pos, self.global_home)
```

#### 3. Set grid start position from local position
When constructing the grid representation, the world is discretized to reduce the size of the grid space so that the minimum data points form the upper left of the grid.  
This means that the relative center is the offset of each direction in the NED frame.
Here we obtain the offsets for determining our global position relative to the grid world:
```
    grid, north_offset, east_offset = create_grid(data, TARGET_ALTITUDE, SAFETY_DISTANCE)

```

#### 4. Set grid goal position from geodetic coords
Once the relative offsets are determined, the actual local position is added flexibility to the desired goal location. 
Should be able to choose any (lat, lon) within the map and have it rendered to a goal location on the grid.

```
   local_pos = global_to_local(global_pos, self.global_home)
```

#### 5. Modify A* to include diagonal motion (or replace A* altogether)
In this project the search algorithm has been replaced by an extended version of A*.
define cost of diagonal motion to sqrt(2) by adding:

    NE = (-1, 1,np.sqrt(2))
    SE = ( 1, 1,np.sqrt(2))
    SW = ( 1,-1,np.sqrt(2))
    NW = (-1,-1,np.sqrt(2))

and `valid_actions`

    if x - 1 < 0 or y - 1 < 0 or grid[x-1,y-1] == 1:
        valid_actions.remove(Action.NW)
    if x - 1 < 0 or y + 1 > m or grid[x-1,y+1] == 1:
        valid_actions.remove(Action.NE)
    if x + 1 > n or y - 1 < 0 or grid[x+1,y-1] == 1:
        valid_actions.remove(Action.SW)
    if x + 1 > n or y + 1 > m or grid[x+1,y+1] == 1:
        valid_actions.remove(Action.SE)

#### 6. Cull waypoints 
To remove redundant waypoints, I have implemented Prune path to minimize number of waypoints.
This was implemented by introducing two global function in the motion_planning.py.

### Execute the flight
#### 1. Does it work?
It works!


