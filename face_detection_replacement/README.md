# Face Detection and Replacement
The goal of this package is **automatic detection and replacement of faces in videos**. Given a test video, the package can automatically replace a target face. Ideally, it can try to achieve this task in the most stable way possible.       


Introduction
------------
In general, the seamlessly face replacement is a non-trivial process. Here we define some terminologies for a better representation:      
* face(s) in the video **=** target face(s);
* face(s) used for replacement **=** replacement face(s). 

The whole package is extending previously explored concepts such as simple affine, warp, blending, or additional deformity using TPS. You are of course not limited to these and are encouraged to experiment with any other suitable approaches you may ﬁnd.      

**_Note_**: If you are using multiple faces for training, you should match against all of them and use the one that requires the least morphing.


Algorithms
----------
The pipeline of the whole algorithm is straightforward. Below shows the pipeline which I use, and feel free to add any step that makes sense to you.

1. **_Replacement Face(s) Selection_**             
    First, it's necessary to build a model of the replacement face. You can use a single, well aligned photo, or a sequence under diﬀerent poses. 

2. **_Image Feature Extraction_**             
    In order to obtain a better face detection performance in each frame, extracting local features is pretty important. You can refer to some popular used features like HoG, Shape Context features, Haar-like features and Harris Corners. Also, feel free to use some 3rd party libraries for more features.
    
3. **_Target Face Detection and Keypoints Localization_**          
    Attempt to locate instance(s) of faces in the test video using an automated detector. Based on the features extracted in the previous step, combine with some advanced approaches like the Eigen face correlation computation. Here we refer to the 3rd party library [Face ++](https://www.faceplusplus.com.cn/) to help the face detection task.

4. **_Face Warping_**        
    For each instance of the target faces, ﬁnd a deformable image transform that warps the reference face to the detected instance. Apply this transform to the replacement face. An affine warp (use Triangulation mesh and barycentric coordinates) or a TPS warp with low number of keypoints may be appropriate. 

5. **_Face Replacement_**       
     Compute the convex hull of the detected face and the warped replacement face. Replace the convex hull of the detected face with the convex hull of the warped replacement face in the test image. 

6. **_Refinement_**       
    In addition, here are some directions to make the replacement more natrual and smooth.
    * Use Laplacian image blending/Gradient domain **blending** to get a seamless integration of the new face.
    * Find the appearance difference of the detected face to the warped version of the detected face. This colud be a per-pixel additive offset to **compensate for shadowing and lighting** of the target face. Apply this brightnes/color offset to the warped replacement face to achieve a better appearance match.
    * When dealing with videos, you might expect the faces in the video to **move**. Therefore, exploring methods for motion compensation is highly recommended.

**Pipeline Graph**
<div align=center>
 <img width="700" height="310" src="./source images/pipeline.png", alt="pipeline"/>
</div>



Execution
---------
All source codes are stored in the folder **_src_** and the main file is **_demo.m_**. For thes test, you can use images in the folder **_source images_**, or feel free to choose your own images with a somehow distinguishable face regions for a better performance. 


Result
------
All generated videos are stored in the folder **_result_**. Also, the file **_report.pdf_** covers more algorithm details and analyses. Below shows a successful face detection and replacement for one single frame in a test video (**Blue contour represents the detected face region**).

* **_Source Image_**
<div align=center>
 <img width="700" height="350" src="./result/im_source.png", alt="source face"/>
</div>

* **_Target Image (two faces)_**
<div align=center>
 <img width="650" height="330" src="./result/im_target.png", alt="target face"/>
</div>

* **_Replaced Image_**
<div align=center>
 <img width="550" height="300" src="./result/im_res.png", alt="result face"/>
</div>
