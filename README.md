# CV Detection and Tracking of Golf Ball from Smartphone Videos
The objective of this project is to use computer vision techniques on slow-motion smartphone golf videos to emulate launch monitor statistics. High-end launch monitors use radar technology or high-speed cameras to track the movement of the golf ball and club, measuring parameters like launch angle, ball speed, spin rate, and clubhead speed. These tools have revolutionized training and golf club fittings by offering players insights that can refine their technique and strategy, as well as match the appropriate equipment to their swings. However, these tools come at a significant cost, often out of reach for the average golfer. The entry-level launch monitors with roughly 98% accuracy are typically over [$300](https://topshelfgolf.com/collections/golf-launch-monitors), while the most accurate high-end launch monitors, like the Trackman or GCQuad, are closer to [$20,000](https://topshelfgolf.com/collections/golf-launch-monitors).

This project is still a work in progress, as the main obstacles faced so far are data quality and quantity. Here, the data consists of 93 golf shots filmed by multiple smartphones and with launch data obtained from using a Swing Caddie entry-level launch monitor. The project is comprised of a detection model and a prediction model. Using Yolov8, the detection model identifies and tracks the movements of the golf club and ball throughout the video. The x and y-coordinates of the objects are then used as inputs to a prediction model to estimate the golf ball launch statistics.

## Data
The data was gathered by filming 150 golf shots on two separate iPhones. The smartphones were placed on tripods 6" off the ground and 6' away at two angles: a face-on view in front of the player and a down-the-line view directly behind the ball. Each video began by using a Bluetooth clicker to start both videos at the same time.

![](/images/dtl_sequence.png)

![](/images/fo_sequence.png)

Due to some issues with the launch monitor and the clicker, 93 shots were successful in capturing both video angles and the launch monitor data. The small dataset hurt the ability to accurately build the prediction model and is a top priority for future steps. The images were uploaded to Google Drive for storage and retrieval and prepared using the Python package CV2. This package prepared the video to be analyzed frame-by-frame for Yolov8. After initial experiments using the pre-trained model on the [COCO](https://cocodataset.org/#home) dataset, I used a sample of 300 frames of images taken from the video data and manually annotated bounding boxes to the club and ball using [cvat.ai](https://www.cvat.ai/).

![](/images/cvat.ai.png)

After this, the data was ready for a Yolov8 model to train on the dataset.

## Methods
The cvat.ai tool can export the annotation in YOLO 1.1 format, so the data was immediately ready to be trained by Yolov8. The model was able to find the greatest success when the image size was at least 1024. Data augmentation was used during training (left image), and the model was trained to label the object and assign a confidence level probability (right image).


|![](/images/sample_training.jpg)|![](/images/validation_image.jpg)|
|:-:|:-:|
|Training Batch with Data Augmentation|Validation Batch with Confidence Level Probability|

The resulting model was used on the 186 videos (93 golf shots across two angles) to detect and track golf ball and club movement throughout each frame of the video. From there, the data was analyzed to find the most common bounding box region for both the golf ball and club to identify the golf ball's starting point, since some false positives would occasionally show up. With the initial location of the ball identified, the data was then adjusted so the golf ball's starting location was (x,y) = (0,0), and all other data points were adjusted from that location. The first frame after impact was identified by finding the golf ball's first tracked location that deviated far enough from either the x1 or y1 location depending on the camera angle. The data below shows the golf ball (class_id = 1) and golf club (class_id = 0), the xy-coordinates of the bounding box (from upper left to lower right), probability (confidence), and tracking (object_id) by video and frame near club impact of video 1.

![](/images/golf_ball_club_data.png)


Once the impact location was identified, the data was simplified to the five frames just before the ball was struck and the seven frames after impact. Multiple datasets were tested due to the nature of the data. The initial belief for the prediction model was a Bidirectional RNN would find the most success to take advantage of the sequential nature of the data. 

## Results

Unfortunately, the prediction model was not able to achieve the desired success. The metrics did not indicate the models were much better than choosing the mean distance of the golf shot as the prediction. However, the detection model was successful in detecting and tracking golf club and ball movements throughout the video, which should lead to improved models in the future with a larger dataset and improved camera placement. The trained Yolov8 model achieved 99.8% correct classification for predicting the objects, an improvement on previous models that were closer to 76%. For the video data, and within the 12 frames used for prediction, the model was 99.1% accurate in correctly identifying the objects

![](/images/detection_model_accuracy_12_frames.png)

The objective of this project is to utilize object detection and tracking techniques in computer vision to provide golf ball launch statistics from slow-motion videos filmed on smartphone devices






![](/images/dtl_sequence.png)
