# TF-Unity

Scripts for accessing TensorFlow API in Unity3d. Supports image classification and object detection (SSD or Tiny YOLOv2).

## Installation

#### 1) Install Unity TensorFlow Plugin.

> For details refer to [Using TensorFlowSharp in Unity (Experimental)](https://github.com/Unity-Technologies/ml-agents/blob/4abb4a13e5ad65c4311efd863508929cedd0b79c/docs/Using-TensorFlow-Sharp-in-Unity.md).

1. Download and import Unity TensorFlow Plugin ([Download here](https://s3.amazonaws.com/unity-ml-agents/0.5/TFSharpPlugin.unitypackage)).

2. Go to `Edit` -> `Player Settings` and add `ENABLE_TENSORFLOW` to the `Scripting
Define Symbols` for each type of device you want to use (**`PC, Mac and Linux
Standalone`**, **`iOS`** or **`Android`**).

3. iOS additional instructions for building

* Once you build the project for iOS in the editor, open the .xcodeproj file within the project folder using Xcode.
* Set up your ios account following the [iOS Account setup page](https://docs.unity3d.com/Manual/iphone-accountsetup.html).
* In **Build Settings** > **Linking** > **Other Linker Flags**:
  * Double click on the flag list to expand the list
  * Add `-force_load`
  * Drag the library `libtensorflow-core.a` from the **Project Navigator** on
    the left under `Libraries/ML-Agents/Plugins/iOS` into the flag list, after
    `-force_load`. 

#### 2) Copy the `TensorFlow` folder in this repo to the `Assets` folder of the Unity project.

#### 3) Copy the `Models` folder in this repo to the `Assets` folder of the Unity project.

## Usage

### Image Classification

- Initialize classifier:

```C#
var classifier = new Classifier(
  model,                                       // TextAsset
  labels,                                      // TextAsset
  input: "input",                              // Optional, defaults to "input"
  output: "MobilenetV1/Predictions/Reshape_1", // Optional, defaults to "output"
  height: 300,                                 // Optional, defaults to 224
  width: 300,                                  // Optional, defaults to 224
  mean: 127.5f,                                // Optional, defaults to 127.5f
  std: 127.5f                                  // Optional, defaults to 127.5f
);
```

- Classify image:

```C#
var outputs = classifier.Classify(
  texture,             // Texture2D
  numResults: 1,       // Optional, defauls to 5
  angle: 90,           // Optional, defauls to 0
  threshold: 0.05f,    // Optional, defauls to 0.1f
  flip: Flip.VERTICAL, // Optional, default to Flip.NONE
);
```

- Output format:

```C#
[ // List
  { // KeyValuePair
    "apple": 0.1 // string: float
  },
  ......
]
```

### Object Detection

- Initialize detector:

```C#
var detector = new Detector(TextAsset 
  modelFile,                   // TextAsset
  labelFile,                   // TextAsset
  model: DetectionModels.YOLO, // Optional, defaults to DetectionModels.SSD
  input: "image_tensor",       // Optional, defaults to "input"
  output: "output",            // Optional, defaults to "output"
  height: 300,                 // Optional, defaults to 300
  width: 300,                  // Optional, defaults to 300
  mean: 127.5f,                // Optional, defaults to 127.5f
  std: 127.5f                  // Optional, defaults to 127.5f
  blockSize: 32,               // Used in YOLO only, defaults to 32
  numBoxesPerBlock: 5,         // Used in YOLO only, defaults to 5
  anchors: []                  // Used in YOLO only, defaults to [0.57273,0.677385,1.87446,2.06253,3.33843,5.47434,7.88282,3.52778,9.77052,9.16828]
);
```

- Detect objects on image:

```C#
var outputs = detector.detect(
  texture,               // Texture2D
  numResultsPerClass: 5, // Optional, defauls to 1
  angle: 90,             // Optional, defauls to 0
  threshold: 0.5f,       // Optional, defauls to 0.2f
  flip: Flip.VERTICAL,   // Optional, default to Flip.NONE
);
```

- Output fomart:

`x, y, w, h` are between [0, 1]. You can scale `x, w` by the width and `y, h` by the height of the image.

```C#
[ // List
  { // Dictionary
    "detectedClass": "hot dog", // string: string
    "confidenceInClass": 0.123, // string: float
    "rect": { // Dictionary
      "x": 0.15, // string: float
      "y": 0.33,
      "w": 0.80,
      "h": 0.27
    }
  },
  ......
]
```

### Utils

- Draw boxes and labels from object detection output:

```C#
Utils.DrawOutput(
  outputs, 
  Screen.width,  // Scale width
  Screen.height, // Scale height
  Color.yellow
);
```

- Draw labels from image classification output:
```C#
Utils.DrawOutput(
  outputs, 
  new Vector2(20, 20), // Position
  Color.red
);
```

## Example

Refer to [TF-Unity-ARFoundation](https://github.com/shaqian/TF-Unity-ARFoundation).