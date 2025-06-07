# Sensors and Vision

This page covers the various sensors and vision systems used in our robot, including encoders, gyroscopes, cameras, and computer vision processing.

## Encoders

### Basic Encoder Usage

```java
// Example encoder implementation
public class Drivetrain extends SubsystemBase {
    private final Encoder m_leftEncoder = new Encoder(
        Constants.Drive.kLeftEncoderPortA,
        Constants.Drive.kLeftEncoderPortB
    );
    
    private void configureEncoders() {
        m_leftEncoder.setDistancePerPulse(Constants.Drive.kEncoderDistancePerPulse);
        m_leftEncoder.setReverseDirection(true);
    }
    
    public double getLeftDistance() {
        return m_leftEncoder.getDistance();
    }
}
```

### Encoder Best Practices

1. **Configuration**
   - Set correct distance per pulse
   - Configure direction
   - Implement zeroing methods

2. **Error Handling**
   - Check for disconnected encoders
   - Implement timeout detection
   - Use multiple sensors when possible

## Gyroscope

### Basic Gyro Usage

```java
// Example gyro implementation
public class Drivetrain extends SubsystemBase {
    private final AHRS m_gyro = new AHRS(SPI.Port.kMXP);
    
    public double getHeading() {
        return m_gyro.getYaw();
    }
    
    public void resetGyro() {
        m_gyro.reset();
    }
}
```

### Gyro Best Practices

1. **Calibration**
   - Calibrate on robot startup
   - Check for calibration errors
   - Implement zeroing methods

2. **Usage**
   - Use for field-oriented drive
   - Implement turn-to-angle commands
   - Consider drift compensation

## Computer Vision

### Camera Setup

```java
// Example camera setup
public class Vision extends SubsystemBase {
    private final UsbCamera m_camera;
    private final VisionPipeline m_pipeline;
    
    public Vision() {
        m_camera = CameraServer.getInstance().startAutomaticCapture();
        m_camera.setResolution(640, 480);
        m_camera.setFPS(30);
        
        m_pipeline = new VisionPipeline();
        m_camera.setPipeline(m_pipeline);
    }
}
```

### Vision Processing

```java
// Example vision pipeline
public class VisionPipeline extends Pipeline {
    @Override
    public void process(Mat input) {
        // Convert to HSV
        Imgproc.cvtColor(input, input, Imgproc.COLOR_BGR2HSV);
        
        // Threshold for target color
        Core.inRange(input, 
            new Scalar(Constants.Vision.kHueMin, Constants.Vision.kSatMin, Constants.Vision.kValMin),
            new Scalar(Constants.Vision.kHueMax, Constants.Vision.kSatMax, Constants.Vision.kValMax),
            input
        );
        
        // Find contours
        List<MatOfPoint> contours = new ArrayList<>();
        Imgproc.findContours(input, contours, new Mat(), Imgproc.RETR_EXTERNAL, Imgproc.CHAIN_APPROX_SIMPLE);
    }
}
```

### Vision Best Practices

1. **Camera Setup**
   - Use appropriate resolution
   - Set correct exposure
   - Implement error handling

2. **Processing**
   - Use efficient algorithms
   - Implement filtering
   - Consider multiple targets

3. **Integration**
   - Use NetworkTables for communication
   - Implement target tracking
   - Consider latency

## Sensor Fusion

### Combining Multiple Sensors

```java
// Example sensor fusion
public class Drivetrain extends SubsystemBase {
    private final Encoder m_encoder;
    private final AHRS m_gyro;
    
    public Pose2d getPose() {
        return new Pose2d(
            m_encoder.getDistance(),
            0,
            Rotation2d.fromDegrees(m_gyro.getYaw())
        );
    }
}
```

## Best Practices

1. **Sensor Selection**
   - Choose appropriate sensors
   - Consider redundancy
   - Plan for failure modes

2. **Data Processing**
   - Implement filtering
   - Handle noise
   - Consider latency

3. **Integration**
   - Use sensor fusion
   - Implement error checking
   - Document calibration procedures

## Example Repository

Our [example repository](https://github.com/COMETRobotics/example-robot-code) includes implementations of these sensor and vision systems. Key files to review:

- `subsystems/Drivetrain.java` - Encoder and gyro examples
- `subsystems/Vision.java` - Camera and vision processing
- `util/SensorFusion.java` - Sensor fusion examples

## Next Steps

- Review the [Autonomous Programming](Autonomous-Programming) page
- Learn about [Best Practices](Best-Practices)
- Practice implementing sensor systems in the example repository 