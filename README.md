# CV Detection and Tracking of Golf Ball from Smartphone Videos
The objective of this project is to use computer vision techniques on slow-motion smartphone golf videos to emulate launch monitor statistics. High-end launch monitors use radar technology or high-speed cameras to track the movement of the golf ball and club, measuring parameters like launch angle, ball speed, spin rate, and clubhead speed. These tools have revolutionized training and golf club fittings by offering players insights that can refine their technique and strategy, as well as match the appropriate equipment to their swings. However, these tools come at a significant cost, often out of reach for the average golfer. The entry-level launch monitors with roughly 98% accuracy are typically over [$300](https://topshelfgolf.com/collections/golf-launch-monitors), while the most accurate high-end launch monitors, like the Trackman or GCQuad, are closer to [$20,000](https://topshelfgolf.com/collections/golf-launch-monitors).

This project is still a work in progress, as the main obstacles faced so far are data quality and quantity. Here, the data consists of 93 golf shots filmed by multiple smartphones and with launch data obtained from using a Swing Caddie entry-level launch monitor. The project is comprised of a detection model and a prediction model. Using Yolov8, the detection model identifies and tracks the movements of the golf club and ball throughout the video. The x and y-coordinates of the objects are then used as inputs to a prediction model to estimate the golf ball launch statistics.

## Data
The data was gathered by filming 150 golf shots on two separate iPhones. The smartphones were placed on tripods 6" off the ground and 6' away at two angles: a face-on view in front of the player and a down-the-line view directly behind the ball. Each video began by using a Bluetooth clicker to start both videos at the same time.

![](/images/dtl_sequence.png)

![](/images/fo_sequence.png)




The objective of this project is to utilize object detection and tracking techniques in computer vision to provide golf ball launch statistics from slow-motion videos filmed on smartphone devices





![](/images/dtl_sequence.png)
