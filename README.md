# tec-Ternary

## Tri Logic


As an 8-bit CPU robot, SQL can be thought of as a set of instructions that allow the robot to perform database operations. The robot can use SQL instructions to read, write, update, and delete data from the database.

When the robot encounters missing data in the database, it can use ternary logic to handle comparisons with NULL values. In the CPU's register, NULL values can be represented as a special value, such as 0xFF, to indicate that the data is missing.

The robot can also use special instructions to handle NULL values, such as a conditional jump instruction that checks for NULL values before making a decision.

To ensure consistent handling of NULL values, the CPU can use a standard protocol for NULL representation and comparison. This protocol can include rules for set operations like UNION or INTERSECT, to ensure that NULL values are treated consistently across different database operations.

Overall, SQL provides a powerful set of instructions for the 8-bit CPU robot to manage and manipulate data in a database, even when some data is missing or incomplete.

A simple 8-bit database that can run on a Z80 CPU is typically implemented using flat files or indexed files. Here are two examples:

Flat file database:
A flat file database is a simple way to store data in a flat file with fixed-length records. Each record in the file represents a row in the database table, and the fields in the record represent the columns in the table. The Z80 CPU can read and write to the file using standard file I/O instructions.

Indexed file database:
An indexed file database uses an index file to improve the performance of data access. The index file contains pointers to the data file, allowing the Z80 CPU to quickly retrieve data based on a key field. The index file can be implemented as a binary tree or a hash table, depending on the complexity of the database and the available memory.

Both of these database types can be implemented in assembly language on a Z80 CPU, with file I/O operations handled using the Z80's standard input/output instructions. While these databases are relatively simple and limited in capacity, they can be useful for small-scale applications that don't require the features and scalability of a full-fledged relational database system.

## Mint
To define an array with 16 bits elements, you can use the syntax array 16bits	with `[`	begins an array definition. When you create an array you get it's address and length	stack <2x address> <2x length>, for eg
```
> [1 2 3 4]
> ctr-p 3368 00004
```
thats a very simple database, but it can represent world data picked up from sensors. etc


## truth tables for the ternary logical operators:

## Ternary OR Operator:
```
A	B	A OR B
T	T	T
T	U	T
T	F	T
U	T	T
U	U	U/U
U	F	U/U
F	T	T
F	U	U/U
F	F	F
```

## Ternary AND Operator:
```
A	B	A AND B
T	T	T
T	U	U/U
T	F	F
U	T	U/U
U	U	U/U
U	F	F
F	T	F
F	U	U/U
F	F	F
```

## Ternary NAND Operator:
```
A	B	A NAND B
T	T	F
T	U	U/U
T	F	U
U	T	U/U
U	U	U/U
U	F	U
F	T	U/U
F	U	U/U
F	F	T
```

## Ternary NOT Operator:
```
A	NOT A
T	F
U	U/U
F	T
```
## Ternary XOR Operator:
```
A	B	A XOR B
T	T	F
T	U	U/U
T	F	T
U	T	U/U
U	U	U/U
U	F	U/U
F	T	T
F	U	U/U
F	F	F
```
Note that in the tables above, "U/U" stands for "Unknown/Unknown," which represents the result when one or both of the operands are unknown in ternary logic.


##  GPIO Motor Controller with Unknown State Handling

`mcus.c`
A basic interface for controlling three motors through General Purpose Input/Output (GPIO) pins using the WiringPi library is provided by this code. It is specifically designed for a Raspberry Pi, but could be adapted for other platforms. 

A high-level overview of what each part of the program does is given:

- The possible states a motor can be in (ON, OFF, or UNKNOWN) are defined by the MotorState Enum. This Enum provides clear, human-readable status values for each motor, and can be easily expanded if more states are required.

- At the beginning of the program, the initializeMotorPins Function is called to initialize the WiringPi library and set the mode of each motor's control pin to OUTPUT.

- The controlMotors Function takes the desired state for each motor as input and sets the appropriate GPIO pin to either HIGH (turn the motor on) or LOW (turn the motor off) depending on the provided state. If the motor state is UNKNOWN, the pin is set to LOW as a default safe behavior.

- The handleUnknownState Function is called when the state of any of the motors is UNKNOWN. Currently, it doesn't perform any actions, but in a real application, it might attempt to determine the actual state of the motors (perhaps by reading sensor data) or trigger some kind of alert or failsafe behavior.

- The main Function serves as the entry point of the program. It first calls initializeMotorPins to set up the GPIO pins. Then, it sets up a hypothetical scenario where the states of the three motors are defined (in this case, the first motor is ON, the second is OFF, and the third is UNKNOWN). It then checks if any of the motors are in an UNKNOWN state and, if so, calls handleUnknownState. If none of the motors are in an UNKNOWN state, it calls controlMotors to apply the desired states to the motors.

In summary, this program provides a template for controlling motors with the ability to handle unknown states. However, as it currently stands, it's not a complete application; it would need to be incorporated into a larger program that provides the necessary context (e.g., why and when the motors should be turned on or off) and additional functionality (e.g., determining the actual state of the motors when their state is UNKNOWN).

The `MotorState` enum has three possible values: `ON`, `OFF`, and `UNKNOWN`. A clearer representation of the state of each motor is achieved by using this enum, as opposed to using a `bool`. 

The `controlMotors` function has been updated to accept `MotorState` arguments, and the `digitalWrite` calls have been modified to only turn on the motors if their state is `ON`.

Finally, in the `main` function, the check for an unknown motor state has been changed to use the `UNKNOWN` enum value. If any of the motors are in an `UNKNOWN` state, the `handleUnknownState` function is called. Otherwise, the `controlMotors` function is called as before.

## Ref
- https://github.com/SteveJustin1963/tec-MINT

