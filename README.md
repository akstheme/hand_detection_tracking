# Hand Gesture Tracking using Harris Features

This MATLAB project demonstrates a method for detecting and tracking hand gestures in a video file. The script utilizes Harris corner detection and point tracking to identify and follow the hand movements across the video frames.

## Features

- **Face Detection**: Initializes by detecting the face in the first video frame.
- **Hand Segmentation**: Segments the hand from the detected face region.
- **Feature Detection**: Uses Harris corner detection to identify key points within the region of interest.
- **Point Tracking**: Tracks the detected points across subsequent video frames.
- **Geometric Transformation**: Applies geometric transformation to maintain the bounding box around the hand.

## Requirements

- MATLAB (with Computer Vision Toolbox)

## Usage

1. Clone the repository:
    ```sh
    git clone https://github.com/yourusername/hand-gesture-tracking.git
    cd hand-gesture-tracking
    ```

2. Ensure you have the required video file (`gesture.mp4`) in the working directory.

3. Run the script:
    ```matlab
    clc;
    close all;
    clear all;

    % Read a video frame and run the face detector.
    videoFileReader = vision.VideoFileReader('gesture.mp4');
    videoFrame = step(videoFileReader);
    [Box,Img]=Hand_seg(videoFrame);
    bbox  = round(Box.Position);

    % Draw the returned bounding box around the detected face.
    videoFrame = insertShape(videoFrame, 'Rectangle', bbox);
    figure; imshow(videoFrame); title('Detected face');

    % Convert the first box into a list of 4 points
    % This is needed to be able to visualize the rotation of the object.
    bboxPoints = bbox2points(bbox(1, :));
    % points = detectMinEigenFeatures(rgb2gray(videoFrame), 'ROI', bbox(1,:));
    points = detectHarrisFeatures(rgb2gray(videoFrame), 'ROI', bbox(1,:));
    % Display the detected points.
    figure, imshow(videoFrame), hold on, title('Detected features');
    plot(points);
    pointTracker = vision.PointTracker('MaxBidirectionalError', 2);

    % Initialize the tracker with the initial point locations and the initial
    % video frame.
    points = points.Location;
    initialize(pointTracker, points, videoFrame);
    videoPlayer  = vision.VideoPlayer('Position',...
        [100 100 [size(videoFrame, 2), size(videoFrame, 1)]+30]);

    oldPoints = points;

    while ~isDone(videoFileReader)
        % get the next frame
        videoFrame = step(videoFileReader);

        % Track the points. Note that some points may be lost.
        [points, isFound] = step(pointTracker, videoFrame);
        visiblePoints = points(isFound, :);
        oldInliers = oldPoints(isFound, :);

        if size(visiblePoints, 1) >= 2 % need at least 2 points

            % Estimate the geometric transformation between the old points
            % and the new points and eliminate outliers
            [xform, oldInliers, visiblePoints] = estimateGeometricTransform(...
                oldInliers, visiblePoints, 'similarity', 'MaxDistance', 4);

            % Apply the transformation to the bounding box points
            bboxPoints = transformPointsForward(xform, bboxPoints);

            % Insert a bounding box around the object being tracked
            bboxPolygon = reshape(bboxPoints', 1, []);
            videoFrame = insertShape(videoFrame, 'Polygon', bboxPolygon, ...
                'LineWidth', 2);

            % Display tracked points
            videoFrame = insertMarker(videoFrame, visiblePoints, '+', ...
                'Color', 'white');       

            % Reset the points
            oldPoints = visiblePoints;
            setPoints(pointTracker, oldPoints);        
        end

        % Display the annotated video frame using the video player object
        step(videoPlayer, videoFrame);
    end

    % Clean up
    release(videoFileReader);
    release(videoPlayer);
    release(pointTracker);
    ```

## Functions

- `Hand_seg`: Custom function to segment the hand from the video frame. Ensure this function is defined or included in your MATLAB path.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- MATLAB documentation and examples provided by MathWorks.
