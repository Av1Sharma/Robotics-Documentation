# Control Systems

This page covers the various control systems used in our robot code, including motor control, PID loops, and sensor feedback.

## Motor Control

### Basic Motor Control

```java
// Example from our Drivetrain subsystem
public class Drivetrain extends SubsystemBase {
    private final TalonFX m_leftMotor = new TalonFX(Constants.Drive.kLeftMotorPort);
    private final TalonFX m_rightMotor = new TalonFX(Constants.Drive.kRightMotorPort);
    
    public void setSpeed(double left, double right) {
        m_leftMotor.set(ControlMode.PercentOutput, left);
        m_rightMotor.set(ControlMode.PercentOutput, right);
    }
}
```

### Motor Configuration

```java
// Example motor configuration
private void configureMotor(TalonFX motor) {
    motor.configFactoryDefault();
    motor.setNeutralMode(NeutralMode.Brake);
    motor.configVoltageCompSaturation(12.0);
    motor.enableVoltageCompensation(true);
}
```

## PID Control

### Basic PID Implementation

```java
// Example PID controller setup
private final PIDController m_pidController = new PIDController(
    Constants.Arm.kP,
    Constants.Arm.kI,
    Constants.Arm.kD
);

public void setArmPosition(double targetPosition) {
    double currentPosition = m_armEncoder.getPosition();
    double output = m_pidController.calculate(currentPosition, targetPosition);
    m_armMotor.set(ControlMode.PercentOutput, output);
}
```

### PID Tuning

1. **Start with P only**
   - Set I and D to 0
   - Increase P until the system oscillates
   - Reduce P to 50% of the oscillating value

2. **Add D to reduce oscillation**
   - Start with a small D value
   - Increase until oscillations are minimized

3. **Add I to eliminate steady-state error**
   - Start with a small I value
   - Increase until steady-state error is eliminated

## Sensor Integration

### Encoders

```java
// Example encoder setup
private final Encoder m_encoder = new Encoder(
    Constants.Drive.kLeftEncoderPortA,
    Constants.Drive.kLeftEncoderPortB
);

private void configureEncoder() {
    m_encoder.setDistancePerPulse(Constants.Drive.kEncoderDistancePerPulse);
    m_encoder.setReverseDirection(true);
}
```

### Limit Switches

```java
// Example limit switch usage
private final DigitalInput m_limitSwitch = new DigitalInput(Constants.Arm.kLimitSwitchPort);

public boolean isAtLimit() {
    return !m_limitSwitch.get(); // Note: DigitalInput returns true when open
}
```

## Motion Profiling

### Trapezoidal Motion Profile

```java
// Example motion profile
private final TrapezoidProfile m_profile = new TrapezoidProfile(
    new TrapezoidProfile.Constraints(
        Constants.Arm.kMaxVelocity,
        Constants.Arm.kMaxAcceleration
    )
);

public void followProfile(double targetPosition) {
    TrapezoidProfile.State goal = new TrapezoidProfile.State(targetPosition, 0);
    TrapezoidProfile.State current = new TrapezoidProfile.State(
        m_encoder.getPosition(),
        m_encoder.getRate()
    );
    
    TrapezoidProfile.State next = m_profile.calculate(0.02, current, goal);
    setArmPosition(next.position);
}
```

## Best Practices

1. **Motor Safety**
   - Always implement current limits
   - Use voltage compensation
   - Implement soft limits

2. **PID Tuning**
   - Start with conservative values
   - Test in a safe environment
   - Document final values

3. **Sensor Usage**
   - Validate sensor readings
   - Implement error checking
   - Use multiple sensors when possible

## Example Repository

Our [example repository](https://github.com/COMETRobotics/example-robot-code) includes implementations of these control systems. Key files to review:

- `subsystems/Drivetrain.java` - Motor control examples
- `subsystems/Arm.java` - PID control examples
- `commands/DriveDistance.java` - Motion profiling examples

## Next Steps

- Review the [Sensors and Vision](Sensors-and-Vision) page
- Learn about [Autonomous Programming](Autonomous-Programming)
- Practice implementing control systems in the example repository 