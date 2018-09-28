# Track-Simulation-Tool
The code provided is tested for many, but not all, cases simulating the performance of Formula Student race car with on different tracks. It was a part of a project done by [ASU Racing Team](http://asuracingteam.org/) participating in [FSUK](https://www.imeche.org/events/formula-student) '17. The code formulation will be briefly discussed, and a guide will be provided, if there's an interest to use the code for your own simulation.

## Scope
The scope of this project was to test multiple designs for the aerodynamic kit, to be installed on ASURT Formula Student car, on Autocross track of the competition. the code was slightly improved, as will be explained later, to optmimize the brake design along with the installed aerodynamic kit.

## Assumptions 
A comprmise between computational complexity and solution accuracy is done leading to the following assumptions:
1. Shifting time is zero (in case of ICE car)
2. No transimission losses. (However, it can be considered while providing the Torque vs RPM curve of the engine or the motor)
3. Transition from acceleration to braking is instantenoues. 

## code weaknesses
1. The tire model used while making this code was a simple one that lead to not realistic behaviours while braking, and this was partially overcome by simulating the car as 4-degrees of freedom while braking.
2. The code simulates the car as a point mass while cornering, but it also makes sure that the max velocity of the car while cornering is acceptaple and won't lead to not realistic results (i.e. the speed of the car is controlled so that no skidding or rolling will occur while cornering)

## Systems modeling 
The car systems that are modeled, beside the track, and that will be asked as input for the code are:
1. Aerodynamic kit (e.g, Cd, Cl of the wings)
2. Brake system (e.g, bias ratio, C.G location)
3. Car different parameters (e.g, car sprung/unsprung masses, spring stiffness, etc.)

The car is modeled as point mass during acceleration, and is modeled as 4-degrees of freedom, or "bicycle" model (shown in Figure 1) during braking. The reason for that is shown in Figure 3. The results showed difference in solution and unphysical behaviors of the point mass model during braking.

### point mass modeling:
Lateral or longitudinal weight transfer, is the amount of change on the vertical loads of the tyres due to the longitudinal acceleration imposed on the centre of gravity (CG) of the car. In other words, it is the amount Weight transfer by which vertical load is increased on the front tyres and reduced from the rear tyres when the car is decelerating.
 
The total weight transfer on the car can be calculated from its free body diagram, as shown in figure 2. In the image, the car is looked from the rear in a right hand turn. Here,  is the lateral acceleration in G units,  is the weight of the car,  is the CG height,  is the track width and and  are the vertical loads on the left and right tyres, respectively.

![capture](https://user-images.githubusercontent.com/27374894/46210505-2ff1f500-c331-11e8-9503-81139d68f11f.PNG)

**Figure 1. lateral weight transfer of a vehichle** 

**in case of** acceleration and cornering, weight transfer was not considered for simplicity, however it was only considered while braking for brake system optimization. 

### 4-degrees of freedom modeling:

![20562638_10213236318492003_839197412_n](https://user-images.githubusercontent.com/27374894/46210573-5f086680-c331-11e8-9b81-287a8748ce62.png)

**Figure 2. 4-degrees of freedom model** 

### performance gap

![figure_1-3](https://user-images.githubusercontent.com/27374894/46210603-72b3cd00-c331-11e8-8162-64771587c97f.png)

**Figure 3. Performance gap between point mass and 4 degrees of freedom model** 

The point mass model exibits discontinuity in the deceleration rate due to that the equations of point mass model, unlike the 4-DOF model, don't take into consideration the time taken to reach the final position, it calculates only the final positions based on discrete inputs. So, this discontinuous manner hapens.

**The not realistic behavoiur was due to simple tire model:** while braking was that there was a discontinous changes in deceleration rate as the output and the inputs for the equations in Figure 1 was dependent. Since the equations in figure 1 did not take into consideration the time taken by the car to reach the final steady state condition after the weigh transfer is done, they were replaced by equations in Figure 2 to account for the transient response for the car to reach the final condition and this partially solved the issue  as seen in Figure 3 

## Code Formulation
The code consists of 4 main functions:
1. Straight road (simulates and outputs the sol. of the car on straight road - acceleration only)
2. Corner (simulates and outputs the sol. of the car on corners acceleration only, with the max velocity through a corner condition)
3. Brake (simulates and outputs the sol. of the car on straight road and corners deceleration)
4. Solver (connects the 3 functions to output the overall solution)

## Code performance
The code provided in this Repo solves each part of the track disregarding the solution history. However, this was found to be slow to finish a simulation due to iterative functions, and functions that ininclude lots of integeration processes, shown in equations in Figure 1. Although it's more accurate to solve for the car performance on track this way, since that the transient response of the 4-degrees of freedom model slightly differs depending on the tarting velocity, another approach was presented that made the code 10 times faster.

**What is the other approach?**
The other approach which is provided through the Jupyter notebook file, is to calculate the universal solution for the car accelerating from zero to maximum velocity, and decelerating from max velocity to zero. This solution is the used to update the solution throughout the track without actually having to calculate it again. For instance, if the car is braking from velocity = 50 km/hr to velocity = 20 km/hr, it will directly extract this part of the universal solution without having to calculate it again.

## A guide to use the code
1. Download the excel file (Car specs) and add all the required inputs (some of the inputs are optional and all explained in the excel sheet)
2. Use the Juypyter notebook provided (Track simulation) to run the code and provide the path to the excel file in the second cell or upload it without changing its name.
3. Run all cells and the results will be saved to a csv file

* Note that the jupyter notebook provided is the only version that is adjusted to extract the values from the excel sheet

## Optional
While giving in input to the excel sheet 2 things are optional:
1. In case of simulating performance of car with wings, you can choose either to make the wings active or static. Static wings will keep Cd and Cl constant on any part of the track. However, the active wings option, will take extra inputs which is the minumum drag position values of Cd and Cl of the wing, and will be used only in straight roads to maximize car acceleration.
2. You can choose whether or not to run the 4-DOF model which will require extra inputs if you want to run it.
