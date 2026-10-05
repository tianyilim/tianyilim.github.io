---
layout: post
title: "The Posters of IROS 2026"
date:   2026-10-05 00:00:00 +0000
categories: conferences
comments: true
---

_a localisation/perception field roboticist's perspective_

---

I attended IROS this year in part to present my [masters' thesis work](https://tianyilim.github.io/2go_slam_website/). Here are some posters from various fields which I found particularly interesting.

## Robot Localisation

Having focused more on deploying odometry/SLAM in robots recently, I've found that chasing marginal gains in RTE/ATE isn't as helpful as making localisation systems robust, lightweight and easily deployable.

The rest of your robotics team doesn't care much about the clever details of the state estimation system - they just want the robot's pose, and they want it cheaply and drift-free. I choose some interesting posters which go in this direction here.

- **[miniVIO: A Minimalist Visual-Inertial Odometry Algorithm with Minimally Inferred Motion Constraints](https://chuchuchen.net/pdf/2026_iros_miniVIO.pdf)** (Yuxiang Peng et al.)

    <details markdown="1">
        
    <summary>
        tldr: an EKF-based VIO which does not estimate landmark positions, resulting in extremely lightweight state estimation without a large drop in accuracy.
    </summary>

    <a href="/assets/IROS2026/miniVIO.jpg"><img width="100%" src="/assets/IROS2026/miniVIO.jpg" alt="miniVIO poster"></a>

    This VIO method goes completely structureless! Instead of estimating both landmark positions and camera/imu state as per all previous VIO methods (OpenVINS/SqrtVINS, VINS-Mono, etc.), they only estimate rotation, position and linear velocity.

    Despite the much simpler state formulation, they show that trajectory estimation accuracy is not significantly affected.
    
    On an original Jetson Nano, the backend's update rate is about 1ms on the EuRoC dataset - 10x faster than the EKF-based SqrtVINS and ~70x faster than the sliding window optimisation-based VINS-Mono, pretty impressive!

    They don't show the runtime for the visual frontend though, which means the overall utility of miniVIO should be taken with a grain of salt. Jetson devices have on-device hardware acceleration for common VIO tasks like KLT and feature detection through [Vision Programming Interface (VPI)](https://developer.nvidia.com/embedded/vpi), so this may not be a major issue, but smaller VIO-sensors like the [Mighty Camera](https://mightycamera.com/) might not find the trade-off worth it.

    </details>

- **[Desc++: Efficient Descriptor Enhancement for Data Association in Existing Visual SLAM Systems](https://arxiv.org/abs/2607.11099)** (TingWei Ou et al.)

    <details markdown="1">
        
    <summary>
        tldr: instead of fully learned image keypoint detectors/descriptors, enhance classical ORB descriptors with a lightweight learned adapter.
    </summary>

    <a href="/assets/IROS2026/DescPlusPlus.jpg"><img width="100%" src="/assets/IROS2026/DescPlusPlus.jpg" alt="Desc++ poster"></a>

    Classical VSLAM frontends typically rely on lightweight, handcrafted ORB or BRISK descriptors for keypoint and landmark correspondence. However, these descriptors have been obsolete since the advent of learned descriptors, like SuperPoint.

    However, since SuperPoint+SuperGlue matching is slow and requires a GPU, they propose an interesting architecture to augment any existing descriptor. They show that their approach is computationally lightweight and effectively boosts matching accuracy. Furthermore, they use their Desc++ to enhance ORB descriptors, and integrate this into various existing VSLAM/VIO systems, demonstrating significant accuracy gains while only costing 5ms extra computation time.

    One comparison I'd like to have seen is with XFeat, a lightweight learned feature detector/descriptor. Compared to Desc++'s claimed 2.4M params for descriptor enhancement, XFeat wraps an entire keypoint detector and descriptor in 1.54M params.

    I'd also like to see a comparison against LightGlue, since both approaches fundementally do the same thing: improves keypoint matching for better correspondence!
    
    </details>

- **[LODESTAR: Degeneracy-Aware LiDAR-Inertial Odometry with Adaptive Schmidt-Kalman Filter and Data Exploitation](https://arxiv.org/abs/2511.09142)** (Eungchang Mason Lee et al.)

    <details markdown="1">
        
    <summary>
        tldr: a Lidar-Inertial Odometry system which selects only reliable past poses / scans into its map, improving robustness against geometric degeneracy.
    </summary>

    <a href="/assets/IROS2026/LODESTAR.jpg"><img width="100%" src="/assets/IROS2026/LODESTAR.jpg" alt="LODESTAR poster"></a>

    LIO is typically much more accurate and drift-free than VIO, yet in geometrically degenerate scenarios, it fails catastrophically. Bad scan registrations get wrongly integrated into the map, causing point cloud smearing; a smeared map degrades point cloud registration, which then compunds the problem.

    This paper proposes to only optimise over a small recent window of states, and hold reliable past states fixed. In a locally degenerate scenario, holding reliable poses fixed prevents the entire state estimator from collapsing.

    LODESTAR is slightly more computationally intensive than FAST-LIO2, but seems to outperform it in a number of datasets. Might be worth taking a look into for robust and reliable LIO. In the meantime, a separate project, [_evalio_](https://github.com/contagon/evalio), is a tool to evaluate different LO/LIO algorithms on some datasets. Looks like it makes comparing and benchmarking LIO more convenient!

    </details>

## Perception and Mapping

Representation learning is trending now, and for good reason. A good environmental representation for building maps of the environment is likewise important. At IROS this year it seemed that people are pushing 3D Gaussians as a scene representation, perhaps motivated by the continuing 3DGS trend from computer vision.

While 3DGS isn't directly applicable to many robotics tasks, these papers take relevant ideas from it for representing the environment in a continuous, probabilistic manner.

- **[Streaming Gaussian Encoding for 4D Panoptic Occupancy Tracking](https://arxiv.org/abs/2606.30754)** (Maximilian Luz et al.)

    <details markdown="1">
        
    <summary>
        tldr: instead of performing object detection and tracking solely in 2D, objects are tracked as Gaussians in 3D, improving representational accuracy, especially with occlusions.
    </summary>

    <a href="/assets/IROS2026/StreamingGaussianEncoding.jpg"><img width="100%" src="/assets/IROS2026/StreamingGaussianEncoding.jpg" alt="Streaming Gaussian Encoding poster"></a>

    In autonomous driving, object tracking is typically done with track-by-detection, where consecutive object detections are used to update the tracker's state. However, in scenarios with occlusion (e.g. lamp posts, fences, other vehicles), 2D detection is degraded, reducing object tracking accuracy.

    This paper proposes to use 3D Gaussians as a persistent scene representation, which helps with cross-frame consistency and map 'completeness', especially from a BEV perspective.

    I like the idea of using Gaussians as an explicit 'map' representation, as they have interpretability and uncertainty baked in. I'm wondering if we could also use this framework outside of autonomous driving scenarios, e.g. in indoor robotics!

    </details>

- **[RayOcc: Occlusion-Aware Ray Occupancy Estimation via Gaussian Mixture Intensity](https://arxiv.org/abs/2607.17660)** (Junho Kim et al.)

    <details markdown="1">
        
    <summary>
        tldr: multimodal depth estimates per camera ray, helping with building 3D volumetric maps in occluded environments.
    </summary>
    
    <a href="/assets/IROS2026/RayOcc.jpg"><img width="100%" src="/assets/IROS2026/RayOcc.jpg" alt="RayOcc poster"></a>

    Most classification problems are cast as unimodal distributions, where there is only "one right answer". Previous methods for depth estimation used a similar paradigm, where there is only "one correct depth" per camera ray.

    However, in scenarios with occlusion, there could be more than one plausible depth per ray, which is better represented as a multimodal distribution. This paper solves this by predicting a Gaussian Mixture Model per-ray, which aids fusing camera detections with lidar depth in autonomous driving scenarios.

    I thought this paper was interesting, as many perception / mapping / scene understanding problems are actually multimodal. For example, I'd like to see this applied to depth reconstruction for windows (many valid depths). It might also be relevant for learned stereo matching, where repetitive textures may lead to multi-modal 'best matches' in each scanline. Accounting for this ambiguity would be useful for assigning uncertainty to the depth network's output.

    </details>

- **[G-EDF-Loc: 3D Continuous Gaussian Distance Field for Robust Gradient-Based 6DoF Localization](https://arxiv.org/abs/2604.04525)** (José E. Maese et al.)

    <details markdown="1">
        
    <summary>
        tldr: instead of an <i>explicit</i> representation of occupancy as in 3D Gaussians, this work models a free space with Gaussians. The result goes toward a unified map representation for both localisation and path planning.
    </summary>

    <a href="/assets/IROS2026/G-EDF-Loc.jpg"><img width="100%" src="/assets/IROS2026/G-EDF-Loc.jpg" alt="G-EDF-Loc poster"></a>

    This work approximates an Euclidean Distance Field (EDF) with a weighted sum of Gaussians. This is interesting, as prior voxel-grid approaches to ESDFs, e.g. voxblox, nvblox, don't scale well to large environments. On the other hand, implicit neural methods don't have guarantees and need GPUs for deployment.

    The proposed method is a tracking / mapping (i.e Lidar Odometry) pipeline solely using Gaussians for the map representation. I include it here in the Perception and Mapping section, as I think its benefits have not yet been fully explored: This is the first time I think localisation and path planning can share the *same* map representation! Previously, LIO methods prefer to maintain a voxel grid of past points, while path planning methods separately use either a height map or an ESDF.
    
    This is still very new work, it would be interesting to see what the authors come up with next!

    </details>

## Embodied AI

Scene Understanding is something I'm actively working on at the moment, and, as with the previous section, there is a lot we don't know about the optimal way to represent an environment. While 3D Scene Graphs are a good way to summarise the environment for an LLM-based high-level planner to consume, it's uncertain how exactly to build one _online_.

No definitive answers in this conference, but here are some practical works which would work well in any Embodied AI stack.

- **[3D Scene Graph Prediction: Generating Hierarchical Models from Partially Observed Environments](https://arxiv.org/abs/2607.10879)** (Siyi Hu et al.)

    <details markdown="1">
        
    <summary>
        tldr: encode 2D room boundaries into a latent space, and predict room boundaries from this space. This supports faster navigation / exploration in unknown indoors environments.
    </summary>

    <a href="/assets/IROS2026/3DSceneGraphPrediction.jpg"><img width="100%" src="/assets/IROS2026/3DSceneGraphPrediction.jpg" alt="3D Scene Graph Prediction poster"></a>

    Scene Graph completion is an interesting solution to indoor exploration, where a network learns to predict room (or even building) layouts from a partially explored map. This allows robots to move around environments in a more informed manner, speeding up coverage search of indoor environments.

    This method seems to be a practical way to use real-world (noisy) sensor measurements of room boundaries (and labels) for boundary completion. Their network architecture is interesting (more of a diffusion-based approach). Despite only training on the 3D-Front dataset, their method seems to generalise to MP3D environments. 
    
    I'd like to see if their method also works on other indoor layouts (e.g office, conference centers, shopping malls) other than homes. In addition, I wonder if it'd be possible to move layout completion into 3D as well!

    </details>

- **[Room-Mediated Co-occurrence for Zero-Shot Object-Centric Semantic Navigation via Frontier Scoring](https://arxiv.org/abs/2607.25448)** (Adam Scicluna et al.)

    <details markdown="1">
        
    <summary>
        tldr: an object-grounded way to do ObjectNav without invoking a VLM, but only CLIP vector similarity.
    </summary>

    One way to do ObjectNav has been to build (or take) a 3D scene graph and reason over it using an LLM / VLM. While that results in high sucess rates, this is also quite computationally expensive, especially when inference is on the edge. Other methods like OpenFrontier rely on VLM-ranked visual frontiers to explore an environment in search of the target object, which still requires an expensive VLM invocation.

    This method is an elegant and simple approach to ObjectNav: With a local 2d map of objects, compare their embedding vectors to a proxy "lexicon". Then also compare the target object's embedding also to the lexicon. The lexicon should be chosen s.t. words in the lexicon similar to the objects in the environment are also similar to target.

    For example:
    - Target: microwave
    - Objects: TV, stove, bed
    - Lexicon: Bedroom, living room, kitchen
    In this case, kitchen is similar to both stove and microwave, so the agent should search near the stove.

    Note:
    - the lexicon need not be room labels, but also affordances / relations
    - the author used CLIP embeddings, but since this works purely in text space, other embeddings (e.g. BERT) may be better.

    I do like this idea for cheaper, more reactive search.

    <a href="/assets/IROS2026/RoomMediatedCooccurrence.jpg"><img width="100%" src="/assets/IROS2026/RoomMediatedCooccurrence.jpg" alt="Room-Mediated Co-occurrence poster"></a>

    </details>

- **[DejaView: Metric-Free Dynamic Spatial Memory for Mobile Robots in Changing Environments](https://openreview.net/pdf?id=DtTXgEIVJp)** (Juexiao Zhang et al.)

    <details markdown="1">
        
    <summary>
        tldr: using posed keyframes as the map representation, and thus doing away with more fragile metric localisation methods.
    </summary>

    <a href="/assets/IROS2026/DejaView.jpg"><img width="100%" src="/assets/IROS2026/DejaView.jpg" alt="DejaView poster"></a>

    This work categorises "places" as a semantic-topological concept, throwing away any need for metric maps. It is therefore more robust to dynamic environments and to localisation failure. In indoors environments, where there is a lot of structure, this topological concepts are arguably sufficient for a robot to perform its intended purpose, especially using the common-sense reasoning afforded by LLM/VLMs.

    I think becoming robust to localisation failure, and operating more in a semantic/topological world, helps to close the gap between humans and robots.

    </details>

## Other Interesting Papers

- **[AnchorD: Metric Grounding of Monocular Depth Using Factor Graphs](https://arxiv.org/abs/2605.02667)** (Simon Dorer et al.)

    <details markdown="1">
        
    <summary>
        tldr: using Factor Graphs to align patchwise monodepth predictions to metric scale.
    </summary>

    <a href="/assets/IROS2026/AnchorD.jpg"><img width="100%" src="/assets/IROS2026/AnchorD.jpg" alt="AnchorD poster"></a>

    I like dense monocular depth and I like using factor graphs for probabilistic inference. This paper combines both concepts to assign metric scale to depth predictions from monocular depth using sparse (sometimes unreliable) supervision.

    They use affine scaling, plus smoothness and other factors to properly scale depth.

    However, the method doesn't yet run in real-time. I'd also like to see if it competes favourably to learned *depth-guided* methods, which natively take in sparse lidar/depth cam supervision to achieve a similarly metrically grounded result.

    </details>

- **[ELLIPSE: Evidential Learning for Robust Waypoints and Uncertainties](https://arxiv.org/pdf/2603.04585)** (Zihao Dong et al.)

    <details markdown="1">
        
    <summary>
        tldr: imitation learning to generate waypoints to climb stairs, solely from (severely occluded) lidar scan input.
    </summary>

    <a href="/assets/IROS2026/ELLIPSE.jpg"><img width="100%" src="/assets/IROS2026/ELLIPSE.jpg" alt="ELLIPSE poster"></a>

    This method generates waypoints to climb stairs solely from input lidar scans, which may be severely occluded based on the pitch of the robot as it ascends/descends stairs.

    This is cast as an imitation learning problem. The 'GT' waypoints are defined as the robots path as it ascends stairs, as it was teleoperated.

    They collect data on 25 staircases for train/testing. The author also mentioned their method works on slightly curvy staircases! Although spiral staircases remain a challenge (they crashed a Spot down the stairs 😱)

    </details>

- **[Proprioceptive-only State Estimation for Legged Robots with Set-Coverage Measurements of Learned Dynamic](https://arxiv.org/pdf/2603.18308)** (Abhijeet M. Kulkarni et al.)

    <details markdown="1">
        
    <summary>
        tldr: a principled way to calibrate uncertainty for learned joint-inertial odometry for legged robots, even where the dynamics differ from training time.
    </summary>

    <a href="/assets/IROS2026/ProprioceptiveStateEstimation.jpg"><img width="100%" src="/assets/IROS2026/ProprioceptiveStateEstimation.jpg" alt="Proprioceptive-only State Estimation poster"></a>

    Leg-inertial odometry is a useful proprioceptive sensor for legged robots. However, its performance is dependent on each robot and the external environment. This method uses a set-coverage method to constrain the probability mass of the error in a calibrated set, so that any EKF estimator is not overly confident.

    I'll need to revise my mathematics to properly understand what they're doing here, but proper calibration of uncertainty does make a lot of sense!

    </details>