# Hand Gesture Tracking using Harris Features

This MATLAB project demonstrates a method for detecting and tracking hand gestures in a video file. The script utilizes Harris corner detection and point tracking to identify and follow the hand movements across the video frames.

## Features

- **Hand Detection**: Initializes by detecting the hand in the first video frame.
- **Hand Segmentation**: Segments the hand from the detected hand region.
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
   main.m
## Functions

- `Hand_seg`: Custom function to segment the hand from the video frame. Ensure this function is defined or included in your MATLAB path.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- MATLAB documentation and examples provided by MathWorks.
