surroundings = {
    (0, 0): [[0, 0, 0],
             [0, 0, 0],
             [0, 0, 0]]
}

def update(x, y, matrix):
    """Update the surroundings with a new matrix at position (x,y)."""
    surroundings[(x, y)] = matrix

def scan():
    """Scan distances in 8 directions and return as a 3x3 matrix."""
    matrixr = []
    temp = []
    c = 0

    for i in range(8):
        f = get_distance()
        temp.append(f)

        if len(temp) == 3: w
            if c == 2:       
                temp.insert(1, 0) 
            matrixr.append(temp)
            temp = []
            c += 1

        tinybit.turn(45) 

    if temp:
        matrixr.append(temp)

    return matrixr


def get_distance():
    """Get distance reading from ultrasonic sensor."""
    return tinybit.ultrasonicsensorv2()

def move_forward():
    tinybit.moveforward()

def turn(angle=45):
    tinybit.turn(angle)

def check(x, y):
    """Return stored matrix at (x,y), or None if not present."""
    return surroundings.get((x, y))

robot_position = [0, 0] 
robot_direction = 0   
home_position = [0, 0]
low_battery = False 
SAFE_DISTANCE = 10  

def explore(width=5, height=5):
    """
    Explore the environment in a lawnmower pattern (side-to-side).
    width: number of steps per row
    height: number of rows
    """
    global robot_position, robot_direction, low_battery

    direction = 0  
    for row in range(height):
        for step in range(width):
            if low_battery:
                return_to_home()
                return

            # 1. Scan surroundings and update map
            matrix = scan()
            update(tuple(robot_position), matrix)

            # 2. Check forward distance and avoid obstacles
            while get_distance() < SAFE_DISTANCE:
                print("Obstacle detected! Turning...")
                turn(90)
                robot_direction = (robot_direction + 90) % 360

            # 3. Move forward safely
            move_forward()
            robot_position = move_in_direction(robot_position, robot_direction)

            # Check battery
            low_battery = check_battery()

        # After finishing a row, move to next row
        if row < height - 1:
            # Turn to next row direction
            if direction == 0:  # moving right, next row go left
                turn_to(90)  # face down
                move_forward()
                robot_position = move_in_direction(robot_position, robot_direction)
                turn_to(180)  # face left
                robot_direction = 180
                direction = 180
            else:  # moving left, next row go right
                turn_to(90)  # face down
                move_forward()
                robot_position = move_in_direction(robot_position, robot_direction)
                turn_to(0)   # face right
                robot_direction = 0
                direction = 0

def move_in_direction(pos, direction):
    """Update (x,y) based on direction in degrees."""
    x, y = pos
    d = direction % 360
    if d == 0:
        x += 1
    elif d == 90:
        y += 1
    elif d == 180:
        x -= 1
    elif d == 270:
        y -= 1
    return [x, y]

def return_to_home():
    """Return robot to home coordinates (0,0)."""
    global robot_position
    print("Returning home due to low battery...")
    while robot_position != home_position:
        if robot_position[0] > home_position[0]:
            turn_to(180)
            move_forward()
            robot_position[0] -= 1
        elif robot_position[0] < home_position[0]:
            turn_to(0)
            move_forward()
            robot_position[0] += 1

        if robot_position[1] > home_position[1]:
            turn_to(270)
            move_forward()
            robot_position[1] -= 1
        elif robot_position[1] < home_position[1]:
            turn_to(90)
            move_forward()
            robot_position[1] += 1
    print("Returned home!")

def turn_to(angle):
    """Turn robot to a specific direction."""
    global robot_direction
    turn((angle - robot_direction) % 360)
    robot_direction = angle % 360

def check_battery():
    """Return True if battery is low."""
    return tinybit.battery() < 20  
