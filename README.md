# Alarm clock in Java (Training project)

## Overview

In this program, the user sets the alarm time in the specified format.
After that, the console displays the current time, which will be updated until the set time arrives.

## More detailed

First, the user enters the alarm time in the format `HH:MM:SS`, otherwise
an error will be displayed. Next, the entered value will be analyzed using the previously
created `DateTimeFormatter` class object.
```Java
public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("HH:mm:ss");
        LocalTime alarmTime = null;
        String filePath = "Sound-Effect.wav";

        while(alarmTime == null){
            try {
                System.out.print("Enter am alarm time (HH:MM:SS): ");
                String inputTime = scanner.nextLine();

                alarmTime = LocalTime.parse(inputTime,formatter);
                System.out.println("Alarm set for "+alarmTime);
            }
            catch (DateTimeParseException e){
                System.out.println("Invalid format. Please use HH:MM:SS");
            }

        }
        //...
}
```

Next, the analyzed time value is used to create an
`AlarmClock` object. This class implements the `Runnable` interface and overrides the
`run` method, which is used to indicate the current time until
the alarm goes off. This procedure will also be performed using the `Thread` class.
```Java
@Override
    public void run(){

        while (LocalTime.now().isBefore(alarmTime)){
            try {
                Thread.sleep(1000);

                LocalTime now = LocalTime.now();

                System.out.printf("\r%02d:%02d:%02d",
                                    now.getHour(),
                                    now.getMinute(),
                                    now.getSecond());
            }
            catch (InterruptedException e) {
                System.out.println("Thread was interrupted");
            }
        }
        System.out.println("\n*ALARM!!!!!!!!*");
        playSound(filePath);
    }
```

When the countdown ends, the audio playback function starts, which can be
interrupted by pressing Enter.
```Java
private void playSound(String filePath){

        File audiofile = new File(filePath);

        try (AudioInputStream audioStream = AudioSystem.getAudioInputStream(audiofile)){
            Clip clip = AudioSystem.getClip();
            clip.open(audioStream);
            clip.start();
            System.out.print("Press *Enter* to stop alarm");
            scanner.nextLine();
            clip.stop();
            scanner.close();
        }
        catch (UnsupportedAudioFileException e){
            System.out.println("Unsupported audio file format");
        }
        catch (LineUnavailableException e){
            System.out.println("Audio is unavailable");
        }
        catch (IOException e){
            System.out.println("Something went wrong");
        }

    }
```