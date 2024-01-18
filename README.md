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

Unfortunately, the prediction model was not able to achieve the desired success. The metrics did not indicate the models were much better than choosing the mean distance of the golf shot as the prediction. However, the detection model was successful in detecting and tracking golf club and ball movements throughout the video, which should lead to improved models in the future with a larger dataset and improved camera placement. The trained Yolov8 model achieved 99.8% correct classification for predicting the objects, an improvement on previous models that were closer to 76%. Here are the metric results of the trained Yolov8 model based on the sample of 300 images:

![](/images/yolov8_metric_results.png)

For the video data, and within the 12 frames used for prediction, the model was 99.2% accurate in correctly identifying the objects. 

![](/images/detection_model_accuracy_12_frames.png)

Once accounting for misses due to the club partially missing from the frame, the accuracy improves to 99.5%. Other misses were either the club being mistaken for the launch monitor case in the background, or the golf ball not being identified since the clouds in the background were nearly the same color. These errors could be adjusted for in the future with more sample images of these instances and better camera angles. This led to 23 manual adjustments by going to the specific frame and finding the correct coordinates. 

The prediction model struggled to differentiate from guessing the mean carry distance. For such a model to pick up on the complex relationships of the frame-by-frame data, more data is required. Additionally, the xy-coordinates vary due to the inexact bounding box measurements and the limitations of a 240-fps video means only so many frames can be taken when the downswing of a club is about 0.25 seconds. The launch monitor accuracy at 98% means I should probably seek to have a dataset with a wider range of data, as opposed to the carry distance ranging from 161.8 to 196.1 yards.

## Conclusion
An initial concern for the project was whether the model would be able to correctly capture the golf ball and club objects throughout the entirety of the video. Though the prediction models 
were subpar due to the size of the dataset, the foundational piece of the detection and tracking model provides optimism for future iterations of the project. The video was recorded at such a 
distance due to initial concerns for detection capabilities from initial test videos exploring the viability of the project. Furthermore, once the model training began, it took several iterations to 
better understand why the model was unable to detect the golf ball and club at times. Without a high enough image size, the model had a difficult time detecting the objects right after impact 
when they were at their highest speeds. Additionally, without the higher image quality, the objects were more easily mistaken for other objects like the clouds, launch monitor case, and trees in the 
distance. This is somewhat of a concern since the higher image size requires more computing resources and is something that could be further explored in the future.


The tracking model would at times identify false objects, like small white tees flying in the air after impact. This could be addressed with more sample images in the training dataset and 
was worked around in this project by first identifying the true starting location for the ball. Another issue faced with the data is the nature of the 240-fps videos. One video might capture the club when it’s 3” away from contacting the ball, but the next video might capture a similar swing and identify the club when it’s 8” away from the ball and the next frame is when the club 
is contacting the ball. I believe with enough data, these inconsistencies can be sorted out and with improved camera angles, more of the swing can be captured. This would provide a better 
approximation of the parabola of the golf club’s movement, which was used to explore the viability to be used as a variable, as well as the interaction terms between object location, fitted lines or parabolas, and the calculated speed at the location.


![](/images/fitted_parabola_club.png)

Though the prediction model did not provide substantial insights, the quality of the detection and tracking models laid a framework for future iterations of this project. The golf ball 
and club were able to be tracked throughout the frames of the videos. With improved camera angles, distance from the ball, and more data, I believe in the ability to predict the 
launch metrics with precision similar to the lower-end launch metrics. More robust models will need to be created, but with enough data and the correct locations of these objects, it comes 
down to solving for the physics or allowing neural networks to appropriately capture the patterns.

The key to future work is an improved dataset. This consists of gathering closer to 1,000 shots instead of the 93 used for data here. Variability of the golf shots would be larger than this dataset with shot distances ranging from 115 to 215 yards instead of 160 to 196 yards. The quality of the videos would improve with cameras filming horizontally and focused on the right section to capture the entirety of the swing from both angles. With better-quality data, the prediction model should be able to improve. Similar to this analysis, a series of models would be tested to identify the best fit. The bidirectional RNN is expected to be the best fit, and if this is the case, the architecture of such a model would be tested across different parameters. An additional next step would be the use of object segmentation instead of detection. This would result in a model that seeks to determine which pixel belongs to which object and could provide rich data to be used in modeling. 


## Acknowledgements
The YouTube videos and associated GitHub documentation from both [Computer Vision Engineer](https://www.youtube.com/watch?v=jIRRuGN0j5E) and [Muhammad Moin Faisal](https://github.com/MuhammadMoinFaisal/YOLOv8-DeepSORT-Object-Tracking) were extremely helpful in the development of the code for object detection and tracking. The Yolov8 documentation in [Ultralytics](https://docs.ultralytics.com/) was similarly instrumental for detection, tracking, and training.


