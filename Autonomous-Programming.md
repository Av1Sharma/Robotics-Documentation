# Autonomous Programming

This page covers autonomous programming concepts, including routine creation, path planning, and autonomous mode selection.

## Basic Autonomous Structure

### Robot Class Integration

```java
// Example autonomous integration in Robot class
public class Robot extends TimedRobot {
    private Command m_autonomousCommand;
    private RobotContainer m_robotContainer;
    
    @Override
    public void autonomousInit() {
        m_autonomousCommand = m_robotContainer.getAutonomousCommand();
        if (m_autonomousCommand != null) {
            m_autonomousCommand.schedule();
        }
    }
    
    @Override
    public void autonomousPeriodic() {
        CommandScheduler.getInstance().run();
    }
}
```

### Autonomous Command Selection

```java
// Example autonomous command selection
public class RobotContainer {
    private final SendableChooser<Command> m_chooser = new SendableChooser<>();
    
    public RobotContainer() {
        configureAutonomousChooser();
    }
    
    private void configureAutonomousChooser() {
        m_chooser.setDefaultOption("Default Auto", new DefaultAuto());
        m_chooser.addOption("Left Side Auto", new LeftSideAuto());
        m_chooser.addOption("Right Side Auto", new RightSideAuto());
        
        SmartDashboard.putData("Auto choices", m_chooser);
    }
    
    public Command getAutonomousCommand() {
        return m_chooser.getSelected();
    }
}
```

## Path Planning

### Basic Path Following

```java
// Example path following command
public class FollowPath extends CommandBase {
    private final Drivetrain m_drivetrain;
    private final PathPlannerTrajectory m_trajectory;
    private final PPSwerveControllerCommand m_controller;
    
    public FollowPath(Drivetrain drivetrain, String pathName) {
        m_drivetrain = drivetrain;
        m_trajectory = PathPlanner.loadPath(pathName, 
            new PathConstraints(4.0, 3.0));
            
        m_controller = new PPSwerveControllerCommand(
            m_trajectory,
            m_drivetrain::getPose,
            m_drivetrain.getKinematics(),
            new PIDController(0.5, 0, 0),
            new PIDController(0.5, 0, 0),
            new PIDController(0.5, 0, 0),
            m_drivetrain::setModuleStates,
            m_drivetrain
        );
        
        addRequirements(m_drivetrain);
    }
    
    @Override
    public void execute() {
        m_controller.execute();
    }
}
```

### Path Creation

```java
// Example path creation
public class PathGenerator {
    public static void generatePaths() {
        // Create a path from start to end
        PathPlannerTrajectory path = PathPlanner.generatePath(
            new PathConstraints(4.0, 3.0),
            new PathPoint(new Translation2d(0, 0), Rotation2d.fromDegrees(0)),
            new PathPoint(new Translation2d(2, 0), Rotation2d.fromDegrees(0))
        );
        
        // Save the path
        PathPlanner.writePathToFile("StraightPath", path);
    }
}
```

## Complex Autonomous Routines

### Command Groups

```java
// Example command group
public class ComplexAuto extends SequentialCommandGroup {
    public ComplexAuto(Drivetrain drivetrain, Arm arm, Intake intake) {
        addCommands(
            // Drive to first position
            new FollowPath(drivetrain, "Path1"),
            
            // Deploy arm and intake
            new ParallelCommandGroup(
                new DeployArm(arm),
                new DeployIntake(intake)
            ),
            
            // Collect game piece
            new CollectGamePiece(intake),
            
            // Return to start
            new FollowPath(drivetrain, "Path2")
        );
    }
}
```

### Conditional Commands

```java
// Example conditional command
public class AdaptiveAuto extends ConditionalCommand {
    public AdaptiveAuto(Drivetrain drivetrain, Vision vision) {
        super(
            // If vision target is visible, follow it
            new FollowVisionTarget(drivetrain, vision),
            // Otherwise, follow default path
            new FollowPath(drivetrain, "DefaultPath"),
            // Condition
            vision::hasValidTarget
        );
    }
}
```

## Best Practices

1. **Path Planning**
   - Use PathPlanner for complex paths
   - Test paths in simulation
   - Consider robot constraints

2. **Command Structure**
   - Use command groups for organization
   - Implement proper error handling
   - Consider timing constraints

3. **Testing**
   - Test in simulation first
   - Use driver station testing
   - Implement logging

## Example Repository

Our [example repository](https://github.com/COMETRobotics/example-robot-code) includes implementations of these autonomous concepts. Key files to review:

- `commands/autonomous/` - Example autonomous routines
- `paths/` - Example path files
- `util/PathGenerator.java` - Path generation utilities

## Next Steps

- Review the [Best Practices](Best-Practices) page
- Learn about [Control Systems](Control-Systems)
- Practice implementing autonomous routines in the example repository 