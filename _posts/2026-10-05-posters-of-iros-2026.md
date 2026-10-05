---
layout: post
title: "The Posters of IROS 2026"
date:   2026-10-05 00:00:00 +0000
categories: conferences
comments: true
---

_a localisation/perception field roboticist's perspective_

---

I attended IROS this year in part to present my [master's thesis work](https://tianyilim.github.io/2go_slam_website/). Here are some posters from various fields that I found particularly interesting.

## Robot Localisation

Having focused more on deploying odometry/SLAM on robots recently, I've found that chasing marginal gains in RTE/ATE isn't as helpful as making localisation systems robust, lightweight and easy to deploy.

The rest of your robotics team doesn't care much about the clever details of the state estimation system – they just want the robot's pose, and they want it cheap and drift-free. Here are some posters that go in this direction.

- **[miniVIO: A Minimalist Visual-Inertial Odometry Algorithm with Minimally Inferred Motion Constraints](https://chuchuchen.net/pdf/2026_iros_miniVIO.pdf)** (Yuxiang Peng et al.)

    <details markdown="1">
        
    <summary>
        tldr: an EKF-based VIO that does not estimate landmark positions, resulting in extremely lightweight state estimation without a large drop in accuracy.
    </summary>

    <a href="/assets/IROS2026/miniVIO.jpg"><img width="100%" src="/assets/IROS2026/miniVIO.jpg" alt="miniVIO poster"></a>

    This VIO method goes completely structureless! Instead of estimating both landmark positions and camera/IMU state, as previous VIO methods do (OpenVINS/SqrtVINS, VINS-Mono, etc.), it only estimates rotation, position and linear velocity.

    Despite the much simpler state formulation, the authors show that trajectory estimation accuracy is not significantly affected.

    On an original Jetson Nano, a backend update takes about 1 ms on the EuRoC dataset – 10x faster than the EKF-based SqrtVINS and ~70x faster than the sliding-window optimisation-based VINS-Mono. Pretty impressive!

    They don't report the runtime of the visual frontend, though, so the overall speed-up should be taken with a pinch of salt. Jetson devices have hardware acceleration for common VIO tasks like KLT tracking and feature detection through NVIDIA's [Vision Programming Interface (VPI)](https://developer.nvidia.com/embedded/vpi), so this may not be a major issue there, but on smaller VIO sensors like the [Mighty Camera](https://mightycamera.com/) the trade-off might not be worth it.

    </details>

- **[Desc++: Efficient Descriptor Enhancement for Data Association in Existing Visual SLAM Systems](https://arxiv.org/abs/2607.11099)** (TingWei Ou et al.)

    <details markdown="1">
        
    <summary>
        tldr: instead of fully learned keypoint detectors/descriptors, enhance classical ORB descriptors with a lightweight learned adapter.
    </summary>

    <a href="/assets/IROS2026/DescPlusPlus.jpg"><img width="100%" src="/assets/IROS2026/DescPlusPlus.jpg" alt="Desc++ poster"></a>

    Classical VSLAM frontends typically rely on lightweight, handcrafted descriptors such as ORB or BRISK for keypoint and landmark correspondence. These have largely been outclassed by learned descriptors like SuperPoint.

    However, since SuperPoint+SuperGlue matching is slow and requires a GPU, the authors propose an interesting architecture to augment any existing descriptor. They show that their approach is computationally lightweight and effectively boosts matching accuracy. They then use Desc++ to enhance ORB descriptors and integrate it into various existing VSLAM/VIO systems, demonstrating significant accuracy gains at a cost of only ~5 ms of extra computation.

    I'd have liked to see a comparison with XFeat, a lightweight learned feature detector/descriptor. Compared to Desc++'s claimed 2.4M params for descriptor enhancement alone, XFeat fits an entire keypoint detector and descriptor into 1.54M params.

    I'd also like to see a comparison against LightGlue, since both approaches fundamentally aim to do the same thing: improve keypoint matching for better correspondence!

    </details>

- **[LODESTAR: Degeneracy-Aware LiDAR-Inertial Odometry with Adaptive Schmidt-Kalman Filter and Data Exploitation](https://arxiv.org/abs/2511.09142)** (Eungchang Mason Lee et al.)

    <details markdown="1">
        
    <summary>
        tldr: a lidar-inertial odometry system that admits only reliable past poses/scans into its map, improving robustness against geometric degeneracy.
    </summary>

    <a href="/assets/IROS2026/LODESTAR.jpg"><img width="100%" src="/assets/IROS2026/LODESTAR.jpg" alt="LODESTAR poster"></a>

    LIO is typically much more accurate and less prone to drift than VIO, yet in geometrically degenerate scenarios it can fail catastrophically. Bad scan registrations get integrated into the map, causing point cloud smearing; a smeared map degrades subsequent registration, which compounds the problem.

    This paper proposes to optimise only over a small window of recent states, and to hold reliable past states fixed. In a locally degenerate scenario, holding reliable poses fixed prevents the entire state estimator from collapsing.

    LODESTAR is slightly more computationally intensive than FAST-LIO2, but seems to outperform it on a number of datasets. Worth a look if you need robust and reliable LIO. On a related note, a separate project, [_evalio_](https://github.com/contagon/evalio), is a tool for evaluating different LO/LIO algorithms across datasets. It looks like it makes comparing and benchmarking LIO much more convenient!

    </details>

## Perception and Mapping

Representation learning is trending, and for good reason. Choosing a good representation for mapping the environment is just as important. At IROS this year, many people were pushing 3D Gaussians as a scene representation, perhaps carried over from the 3DGS trend in computer vision.

While 3DGS isn't directly applicable to many robotics tasks, these papers borrow relevant ideas from it to represent the environment in a continuous, probabilistic manner.

- **[Streaming Gaussian Encoding for 4D Panoptic Occupancy Tracking](https://arxiv.org/abs/2606.30754)** (Maximilian Luz et al.)

    <details markdown="1">
        
    <summary>
        tldr: instead of performing object detection and tracking solely in 2D, objects are tracked as Gaussians in 3D, improving representational accuracy, especially under occlusion.
    </summary>

    <a href="/assets/IROS2026/StreamingGaussianEncoding.jpg"><img width="100%" src="/assets/IROS2026/StreamingGaussianEncoding.jpg" alt="Streaming Gaussian Encoding poster"></a>

    In autonomous driving, object tracking is typically done with tracking-by-detection, where consecutive object detections are used to update the tracker's state. However, under occlusion (e.g. lamp posts, fences, other vehicles), 2D detection degrades, reducing tracking accuracy.

    This paper proposes using 3D Gaussians as a persistent scene representation, which helps with cross-frame consistency and map 'completeness', especially from a BEV perspective.

    I like the idea of using Gaussians as an explicit 'map' representation, as they have interpretability and uncertainty baked in. I wonder whether this framework could also be used outside autonomous driving, e.g. in indoor robotics!

    </details>

- **[RayOcc: Occlusion-Aware Ray Occupancy Estimation via Gaussian Mixture Intensity](https://arxiv.org/abs/2607.17660)** (Junho Kim et al.)

    <details markdown="1">
        
    <summary>
        tldr: multimodal depth estimates per camera ray, helping to build 3D volumetric maps in occluded environments.
    </summary>
    
    <a href="/assets/IROS2026/RayOcc.jpg"><img width="100%" src="/assets/IROS2026/RayOcc.jpg" alt="RayOcc poster"></a>

    Most classification problems are cast as unimodal distributions, where there is only "one right answer". Previous depth estimation methods followed a similar paradigm, with only "one correct depth" per camera ray.

    However, under occlusion there could be more than one plausible depth per ray, which is better represented as a multimodal distribution. This paper addresses this by predicting a Gaussian Mixture Model per ray, which helps fuse camera detections with lidar depth in autonomous driving scenarios.

    I found this paper interesting, as many perception/mapping/scene understanding problems are actually multimodal. For example, I'd like to see this applied to depth reconstruction through windows (many valid depths). It might also be relevant for learned stereo matching, where repetitive textures can lead to multimodal 'best matches' along each scanline. Accounting for this ambiguity would be useful for assigning uncertainty to the depth network's output.

    </details>

- **[G-EDF-Loc: 3D Continuous Gaussian Distance Field for Robust Gradient-Based 6DoF Localization](https://arxiv.org/abs/2604.04525)** (José E. Maese et al.)

    <details markdown="1">
        
    <summary>
        tldr: instead of an <i>explicit</i> representation of occupancy as in 3D Gaussians, this work models free space with Gaussians. This is a step towards a unified map representation for both localisation and path planning.
    </summary>

    <a href="/assets/IROS2026/G-EDF-Loc.jpg"><img width="100%" src="/assets/IROS2026/G-EDF-Loc.jpg" alt="G-EDF-Loc poster"></a>

    This work approximates a Euclidean Distance Field (EDF) with a weighted sum of Gaussians. This is interesting, as prior voxel-grid approaches to ESDFs (e.g. Voxblox, nvblox) don't scale well to large environments, while implicit neural methods offer no guarantees and need GPUs for deployment.

    The proposed method is a tracking and mapping (i.e. lidar odometry) pipeline that uses Gaussians alone as the map representation. I've included it in the Perception and Mapping section because I think its benefits have not yet been fully explored: this is the first time I've seen localisation and path planning share the *same* map representation! Typically, LIO methods maintain a voxel grid of past points, while path planners separately use either a height map or an ESDF.

    This is still very new work; it will be interesting to see what the authors come up with next!

    </details>

## Embodied AI

Scene understanding is something I'm actively working on at the moment, and, as with the previous section, there is a lot we don't know about the best way to represent an environment. While 3D scene graphs are a good way to summarise the environment for an LLM-based high-level planner to consume, it's still unclear how best to build one _online_.

No definitive answers at this conference, but here are some practical works that would fit well into any Embodied AI stack.

- **[3D Scene Graph Prediction: Generating Hierarchical Models from Partially Observed Environments](https://arxiv.org/abs/2607.10879)** (Siyi Hu et al.)

    <details markdown="1">
        
    <summary>
        tldr: encode partially observed 2D room boundaries into a latent space, and predict complete room boundaries from it. This supports faster navigation/exploration in unknown indoor environments.
    </summary>

    <a href="/assets/IROS2026/3DSceneGraphPrediction.jpg"><img width="100%" src="/assets/IROS2026/3DSceneGraphPrediction.jpg" alt="3D Scene Graph Prediction poster"></a>

    Scene graph completion is an interesting approach to indoor exploration, where a network learns to predict room (or even building) layouts from a partially explored map. This allows robots to move around in a more informed manner, speeding up coverage search of indoor environments.

    This method seems to be a practical way to use real-world (noisy) sensor measurements of room boundaries (and labels) for boundary completion. The network architecture is interesting (more of a diffusion-based approach). Despite training only on the 3D-FRONT dataset, the method seems to generalise to MP3D environments.

    I'd like to see whether it also works on non-residential layouts (e.g. offices, conference centres, shopping malls). I also wonder whether it'd be possible to move layout completion into 3D as well!

    </details>

- **[Room-Mediated Co-occurrence for Zero-Shot Object-Centric Semantic Navigation via Frontier Scoring](https://arxiv.org/abs/2607.25448)** (Adam Scicluna et al.)

    <details markdown="1">
        
    <summary>
        tldr: an object-grounded way to do ObjectNav without invoking a VLM, using only CLIP embedding similarity.
    </summary>

    <a href="/assets/IROS2026/RoomMediatedCooccurrence.jpg"><img width="100%" src="/assets/IROS2026/RoomMediatedCooccurrence.jpg" alt="Room-Mediated Co-occurrence poster"></a>

    One way to do ObjectNav has been to build (or take) a 3D scene graph and reason over it with an LLM/VLM. While this achieves high success rates, it is also computationally expensive, especially for inference on the edge. Other methods like OpenFrontier rely on VLM-ranked visual frontiers to explore an environment in search of the target object, which still requires an expensive VLM call.

    This method is an elegant and simple approach to ObjectNav: given a local 2D map of objects, compare each object's embedding to those of a proxy "lexicon", then do the same for the target object. Lexicon entries that are similar to both an observed object and the target indicate where to search.

    For example:

    - Target: microwave
    - Objects: TV, stove, bed
    - Lexicon: bedroom, living room, kitchen

    In this case, "kitchen" is similar to both "stove" and "microwave", so the agent should search near the stove.

    Note:

    - the lexicon need not consist of room labels; it could also contain affordances or relations
    - the authors use CLIP embeddings, but since this works purely in text space, other embeddings (e.g. BERT) may work better

    I do like this idea for cheaper, more reactive search.

    </details>

- **[DejaView: Metric-Free Dynamic Spatial Memory for Mobile Robots in Changing Environments](https://openreview.net/pdf?id=DtTXgEIVJp)** (Juexiao Zhang et al.)

    <details markdown="1">
        
    <summary>
        tldr: using posed keyframes as the map representation, doing away with more fragile metric localisation methods.
    </summary>

    <a href="/assets/IROS2026/DejaView.jpg"><img width="100%" src="/assets/IROS2026/DejaView.jpg" alt="DejaView poster"></a>

    This work treats "places" as a semantic-topological concept, removing the need for metric maps altogether. It is therefore more robust to dynamic environments and to localisation failure. In indoor environments, where there is a lot of structure, this topological representation is arguably sufficient for a robot to perform its intended task, especially with the common-sense reasoning afforded by LLMs/VLMs.

    I think becoming robust to localisation failure, and operating more in a semantic/topological world, helps to close the gap between humans and robots.

    </details>

## Other Interesting Papers

- **[AnchorD: Metric Grounding of Monocular Depth Using Factor Graphs](https://arxiv.org/abs/2605.02667)** (Simon Dorer et al.)

    <details markdown="1">
        
    <summary>
        tldr: using factor graphs to align patchwise monodepth predictions to metric scale.
    </summary>

    <a href="/assets/IROS2026/AnchorD.jpg"><img width="100%" src="/assets/IROS2026/AnchorD.jpg" alt="AnchorD poster"></a>

    I like dense monocular depth, and I like using factor graphs for probabilistic inference. This paper combines both to assign metric scale to monocular depth predictions using sparse (and sometimes unreliable) supervision.

    They combine affine scaling, smoothness and other factors to recover metrically scaled depth.

    However, the method doesn't yet run in real time. I'd also like to see whether it compares favourably with learned *depth-guided* methods, which natively take in sparse lidar/depth camera supervision to achieve a similarly metrically grounded result.

    </details>

- **[ELLIPSE: Evidential Learning for Robust Waypoints and Uncertainties](https://arxiv.org/pdf/2603.04585)** (Zihao Dong et al.)

    <details markdown="1">
        
    <summary>
        tldr: imitation learning to generate waypoints for climbing stairs, solely from (severely occluded) lidar scans.
    </summary>

    <a href="/assets/IROS2026/ELLIPSE.jpg"><img width="100%" src="/assets/IROS2026/ELLIPSE.jpg" alt="ELLIPSE poster"></a>

    This method generates waypoints for climbing stairs solely from input lidar scans, which may be severely occluded depending on the pitch of the robot as it ascends or descends.

    This is cast as an imitation learning problem: the ground-truth waypoints are the robot's path as it is teleoperated up the stairs.

    They collected data on 25 staircases for training and testing. The author also mentioned that their method works on slightly curved staircases, although spiral staircases remain a challenge (they crashed a Spot down the stairs 😱).

    </details>

- **[Proprioceptive-only State Estimation for Legged Robots with Set-Coverage Measurements of Learned Dynamic](https://arxiv.org/pdf/2603.18308)** (Abhijeet M. Kulkarni et al.)

    <details markdown="1">
        
    <summary>
        tldr: a principled way to calibrate uncertainty for learned leg-inertial odometry on legged robots, even when the dynamics differ from those seen during training.
    </summary>

    <a href="/assets/IROS2026/ProprioceptiveStateEstimation.jpg"><img width="100%" src="/assets/IROS2026/ProprioceptiveStateEstimation.jpg" alt="Proprioceptive-only State Estimation poster"></a>

    Leg-inertial odometry is a useful proprioceptive state estimate for legged robots. However, its performance depends on the specific robot and its environment. This method uses set-coverage measurements to constrain the probability mass of the error within a calibrated set, so that the downstream EKF is not overconfident.

    I'll need to revise my maths to properly understand what they're doing here, but proper uncertainty calibration does make a lot of sense!

    </details>
