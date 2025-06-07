# Robot Architecture

This page explains the architecture of our robot code and the design patterns we use.

## Command-Based Programming

Our robot code follows WPILib's command-based programming paradigm, which provides a structured approach to robot control.

### Core Components

1. **Robot Class**
   ```java
   // Main robot class that extends TimedRobot
   public class Robot extends TimedRobot {
       private RobotContainer m_robotContainer;
       
       @Override
       public void robotInit() {
           m_robotContainer = new RobotContainer();
       }
   }
   ```

2. **RobotContainer**
   ```java
   // Central configuration class
   public class RobotContainer {
       private final Drivetrain m_drivetrain = new Drivetrain();
       private final Arm m_arm = new Arm();
       
       public RobotContainer() {
           configureButtonBindings();
           configureDefaultCommands();
       }
   }
   ```

3. **Subsystems**
   ```java
   // Example subsystem
   public class Drivetrain extends SubsystemBase {
       private final TalonFX m_leftMotor = new TalonFX(Constants.Drive.kLeftMotorPort);
       private final TalonFX m_rightMotor = new TalonFX(Constants.Drive.kRightMotorPort);
       
       public void setSpeed(double left, double right) {
           m_leftMotor.set(ControlMode.PercentOutput, left);
           m_rightMotor.set(ControlMode.PercentOutput, right);
       }
   }
   ```

4. **Commands**
   ```java
   // Example command
   public class DriveCommand extends CommandBase {
       private final Drivetrain m_drivetrain;
       private final DoubleSupplier m_left;
       private final DoubleSupplier m_right;
       
       public DriveCommand(Drivetrain drivetrain, DoubleSupplier left, DoubleSupplier right) {
           m_drivetrain = drivetrain;
           m_left = left;
           m_right = right;
           addRequirements(drivetrain);
       }
       
       @Override
       public void execute() {
           m_drivetrain.setSpeed(m_left.getAsDouble(), m_right.getAsDouble());
       }
   }
   ```

## Code Organization

Our codebase is organized into the following packages:

- `frc.robot.commands` - All robot commands
- `frc.robot.subsystems` - Robot subsystems
- `frc.robot.Constants` - Configuration constants
- `frc.robot.util` - Utility classes
- `frc.robot.auto` - Autonomous routines

## Design Patterns

1. **Singleton Pattern**
   - Used for subsystems to ensure only one instance exists
   - Example: Drivetrain, Arm, Intake

2. **Command Pattern**
   - Encapsulates robot actions
   - Allows for command composition and scheduling

3. **Strategy Pattern**
   - Used for autonomous routine selection
   - Allows easy switching between different autonomous modes

## Best Practices

1. **Subsystem Design**
   - Each subsystem should be independent
   - Use proper encapsulation
   - Implement safety limits

2. **Command Design**
   - Commands should be atomic
   - Use command groups for complex sequences
   - Implement proper initialization and cleanup

3. **Constants Management**
   - Use the Constants class for all configuration
   - Document all constants
   - Use meaningful names

## Example Repository Structure

Our [example repository](https://github.com/COMETRobotics/example-robot-code) demonstrates these patterns and practices. Key files to review:

- `Robot.java` - Main robot class
- `RobotContainer.java` - Robot configuration
- `subsystems/` - Example subsystems
- `commands/` - Example commands
- `Constants.java` - Configuration constants

## Next Steps

- Review the [Control Systems](Control-Systems) page
- Learn about [Autonomous Programming](Autonomous-Programming)
- Practice implementing your own subsystems and commands 