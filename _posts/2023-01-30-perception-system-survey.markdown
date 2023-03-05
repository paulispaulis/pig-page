---
layout: post
title:  "Our Perception System: Survey and Pitch"
date:   2023-02-08 04:20:00 +0200
categories: notes
---
<style>
p {
	text-align: justify
}
</style>


### TODO
- type up pitch

---

Contents:

- <A href="#survey">Survey</A>
    - <A href="#survey">Introduction</A>
    - <A href="#curr">Current solutions for unstructured environments</A>
    - <A href="#tech">Technologies</A>
    - <A href="#sota">SOTA research</A>
- <A href="#pitch">Pitch</A>

---

## [Survey](#survey)

This post contains our survey of the current scene in robotics, focused on unstructured (messy) real-world environments, particularly robot vision within those environments.

### Robotics and project pitfalls

A [2022 survey](https://www.tandfonline.com/doi/pdf/10.1080/23311916.2022.2050020) on vision guided robotics systems contained the following diagram. In it we see the process flow of vision-based autonomous robots, the same ones we are working on in this project. A priority we've set for ourselves, is to do our best to work towards something that is actually useful; you might expect that this would always be a background priority for every project, but in computer science research (and likely elsewhere) it is easy to set an interesting question (which only pretends to be useful), answer it patchily (by seeking to appear as having done more and better), then report results... in a way that isn't directly further usable, difficult to reproduce, and likely uses assumptions that make void any chance of usefulness anyways. Thus the project amounts to... most likely nothing. Maybe some publications to be used as padding in the bibliography of other such projects.

![process flow]({{ site.baseurl }}/images/survey/flow.png)
    
### Focus of our project subtask

While we have robots to test on, they are not the ones that would be used in industry, and we will not be making our own. So the final steps of robot control in the real world should not be our focus; while it would look nice in a demo, no one needs our demo robot moving about, they need the "intelligence" within it.

Similarly, path planning is highly dependent on the motion primitives of the specific physical robot, so we move up still.

For localization, we don't have a concrete client use-case, so doing localization of the robot is premature. But, we will undoubtedly want to localize something in our perceived vision field. So localization of objects sticks around.

Perception is the first step, and the one that most describes what we will do in our subtask of the project. We will work on on the robot perception, mapping the environment, and will try out methods of adding semantics to that mapping. Thus we'd be moving towards a hopefully generalisable vision system that maps the environment and recognizes relevant objects within it; this all can then be passed as knowledge to the task planner and later the robot for execution.

For this reason our subtask of the project has gained the name "Perception Systems", with the ambition of moving towards useful and generalisable semantic perception, something that vision based robots need.
    
## [Current solutions for unstructured environments](#curr)

### Agriculture

A [2022 review of applications of machine vision in agricultural robot navigation][agricultural review] gathers the current methods in use for agriculture. This domain is relevant for the similarly messy environment (real-world not pristine factory) and robots that want to move around and accomplish tasks within that environment.

Why use robot vision not some other more reliable technology? One would be global navigation satellite system (GNSS), which some do successfully use! But it becomes tenfold inaccurate in bad weather, struggles to work under canopy, and cannot detect obstacles in an unstructured environment (which outside of the pristine factory... will most definitely happen).

![agriculture]({{ site.baseurl }}/images/survey/complex-agri.png)

What I gathered from the review is that meaningful robot vision is extremely difficult. Achievement wise current agricultural robots can follow crop lines while they are straight and have no obstacles to avoid. But if they are slightly turning or otherwise irregular, or the robot is forced to leave the crop line... it can't reliably return back to task.

An anecdotal example of how the task is more difficult than you might imagine – LIDAR is often seen as a magical cure-all, surely it would be good at seeing the crop lines; turns out that the farming process raises a LOT of dust and LIDAR struggles to remain accurate. So if you are left with just vision – I myself have been in crop fields, looking at them sideways and unable to find anything – pathways, or a friend ducking and hiding.

So the task is difficult. Reduce scope until we find a dragon we can conquer (and hopefully one that is bothering some real people).
    
### Fruit harvesting

Another use-case with computer vision solutions currently in use is harvesting fruit (e.g. picking apples off of trees). It also has an unstructured environment (the branches and apples are placed as they grow). A [2020 paper on apple harvesting (Visual Perception and Modeling for Autonomous Apple Harvesting)](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=9051680) used rgb cameras with additional depth sensors. They developed their own algorithms for detecting apples and branches (not significantly improving upon Mask-rcnn), and the robot has indeed picked apples at a farm. They claimed their robot to be SOTA at the time.

The same authors had the next version of the [robot with LIDAR in 2022 (Accurate Fruit Localisation for Robotic Harvesting using High Resolution LiDAR-Camera Fusion)](https://arxiv.org/pdf/2205.00404.pdf). The better depth sensor allowed for more precise localisation of the apples. They didn't comment on how close to being practicaly useful the robots are. While there are farms with these robots running, they are far from being the industry standard.

![apple picker]({{ site.baseurl }}/images/survey/apple-picker.png)

The same is said in this [2022 review on fruit harvesting robots (Intelligent robots for fruit harvesting: recent developments and future challenges)](https://link.springer.com/article/10.1007/s11119-022-09913-3) "widespread use of harvesting robots in orchards is yet to be seen". Another point to take note of, all of these occasionally impressive results in computer vision are for **predefined classes**. Depending on the use-case the quality of these results can be completely irrelevant for what to expect from results in other tasks (where more general computer vision might be needed).

---

## [Technologies](#tech)

Before moving on to some cutting edge research, I'll introduce some of the relevant components for this task we are approaching.

### Sensors

The input received plays a significant role in the results the algorithm can achieve. Sensor options that have and will appear within this post are:

- regular RGB cameras
- RGB-D cameras (regular cameras with an additional depth sensor; the depth data is fused to each pixel within the camera)
- LIDAR (sensor with no colour information, purely for depth; has to be fused with camera data)
- event cameras (measure change in pixel values not absolute; has potential to help motion blur and dark environments)

### NeRFs

Neural Radiance Fields can generate novel views of scenes after receiving some images of it. It is a form of storage of spatial information, most often used to store colour of voxels, but the data can also be semantic.

### Segmentation (instance, semantic, panoptic)

When creating a perception system we have to confront segmentation, which is to split the scene into some components. Instance segmentation picks out separate entities or objects; semantic segmentation splits the image by what class each pixel belongs to (all tables would be highlighted); panoptic segmentation differentiates each object, also giving each instance the class which it belongs to (thus both instance and semantic segmentation together).

Some form of segmentation has to be used for robot vision. The specifications are dependant on the use case.

### LLMs

Large Language Models have recently gained massive popularity. They leverage enormous amounts of text data to achieve impressive feats. As one might expect some of the impressive results are within textual tasks, but researchers have found creative ways to leverage LLMs within other domains as well (in part robot vision, for example for open-set semantic segmentation – ability to highlight based on any sentence of words, not just picking from pre-defined classes).

## [SOTA research](#sota)

### Google blogs on Robotics and Computer Vision 2023

In February of 2023 Google released multiple blogs reviewing their takes on [robotics](https://ai.googleblog.com/2023/02/google-research-2022-beyond-robotics.html) and [computer vision](https://ai.googleblog.com/2023/01/google-research-2022-beyond-language.html#ComputerVision). 

Some key takeaways that are relevant to our work are that:

- LLMs are superb for everything to do with robotics as they greatly help with real world understanding and reasoning (especially useful for environments made for humans, not for robots)
- the physical robots themselves are still struggling
- the approach of solving vision/understanding and then pairing it with whatever motion priors are available to the robot is a valid one, used in Google research

This gives me reassurance that our idea to focus on the vision not the robot itself is a valid one! And that if done correctly there is hope to later fuse it with an actual robot.

On the vision side the blog mentioned their research into [segmentation](https://ai.googleblog.com/2022/04/pix2seq-new-language-interface-for.html) and new ways they tried of doing novel view synthesis (["NeRFs" done with Transformers](https://ai.googleblog.com/2022/09/view-synthesis-with-transformers.html)), of course praising LLMs throughout. At some point they noted

> A general thrust of this work is to develop techniques that help computers have a better understanding of the 3D world — a longstanding dream of computer vision!

So it seems that the task isn't yet solved and we might have some work to do here.

### Volumetric Instance-Level Semantic Mapping

A 2022 paper titled ["Volumetric Instance-Level Semantic Mapping via Multi-View 2D-to-3D Label Diffusion" by Ruben Mascaro et. al.](https://www.research-collection.ethz.ch/bitstream/handle/20.500.11850/538641/main_preprint.pdf?sequence=1) presented their work towards real-world environment mapping and understanding for robots. They used RGB-D cameras, ran Mask-RCNN on the 2D images, and then mapped the instance and semantic information onto the volumetric map.

As can be seen from the images below the results are quite impressive (though they did not release their code so it can't be validated) but since it uses Mask-RCNN it only works on pre-defined classes. With LLMs avilable this approach is quite naive and limited when you start challenging it with actual tasks.

![3d diff seg 1]({{ site.baseurl }}/images/survey/3d-diff-seg-1.png)
![3d diff seg 2]({{ site.baseurl }}/images/survey/3d-diff-seg-2.png)

### HyperReel

[HyperReel](https://hyperreel.github.io/) is a 4D NeRF made from the footage of a camera-ring (8 cameras fixed on a rig, so that the position of the cameras relative to one another are known). With the footage from the 8 cameras, HyperReel can generate the video from any other viewpoint; amazing quality while the new position is within the camera ring, with the quality quickly degrading when moving outside the ring.

Relevant things to note if we plan to use NeRFs, especially with some 4D feature:

- HyperReel developed a "memory-efficient dynamic volume representation that completely represents a dynamic scene", which would be a useful starting point if we plan to use NeRFs for 4D information storage (e.g. for a reconnaissance robot)
- they achieved a good performance trade-off for rendering; while it shouldn't be too relevant for robotics applications, is good to note
- they found a practical use for geometric primitives. If you look it up on scholar, the most recent large scale comparison of geometric primitives results is a [2022 paper (Chiara Romanengo et al, SHREC 2022: Fitting and recognition of simple geometric primitives on point clouds)][geom prim] where researchers compete fitting primitives on artificial point cloud data. Meanwhile HyperReel found geometric primitives within the NeRF and used them to find planes near which to render! **It might also help with "floaters" if planar geometry is required for rendering** (somewhat ideologically reminds of [RegNeRF](https://m-niemeyer.github.io/regnerf/index.html)).

### E-NeRF

This [Feb 2023 paper titled E-NeRF: Neural Radiance Fields From a Moving Event Camera](https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=10028738&casa_token=dJKMwHRJzdgAAAAA:ouNSy685C3vu7Badra5KzsiVHJA6Kqr7B7vXHyWfb9zegDdzxclENoM2xR9A-O9O4XQ_HVeh) works on the sensor side of NeRFs. They note that "most approaches assume optimal illumination and slow camera motion. These assumptions are often violated in robotic applications". This problem is relevant to our work as well if we plan our work to start from the actual sensors, and even if we start from prepard data we need to be aware of what quality of data can be expected in practical use-cases.

![enerf motion blur]({{ site.baseurl }}/images/survey/enerf.png)

E-NeRF proposes to use an event camera to estimate NeRFs in challenging conditions. It is a wholly different type of camera so it can perform better where regular RGB would fail. If within the project we plan to place actual sensors on robots an event camera might be worth considering. Though the integration of it to a NeRF is a novel research area; while E-NeRF has made their code available, integration would likely still take work. But this is still worthy to note, as the fact that this has been done and researchers are working on it means that motion blur and darker environments will have a sensor solution! So our work can assume it.

### Decomposing NeRF

This [Oct 2022 paper titled Decomposing NeRF for Editing via Feature Field Distillation](https://arxiv.org/abs/2205.15585) deals with open-set 3D segmentation of NeRFs. They do more, as they can afterwards edit the scene as well, but for the robotics perspective the ability to find and highlight any phrase within the 3D space is clearly useful. They used [LSeg](https://arxiv.org/abs/2201.03546) and [Dino](https://arxiv.org/pdf/2104.14294.pdf) as their 2D segmentations, which they then transfered to the 3D space.

This paper did have its issues. The 3D decomposition can only be as good as the 2D they use for input, and LSeg has its limitations. Another is that they admitted they had no solution for "floaters" and that these geometry errors would make the semantic NeRF noisy; it might only be a small visual error in their task, but for robotics it might result in greatly inaccurate action.

![japan lseg]({{ site.baseurl }}/images/survey/japan-lseg.png)

### ConceptFusion

[This is the paper](https://arxiv.org/abs/2302.07241) that we hoped the previous would be! They found a more elegant solution for using CLIP to segment, able to get pixel-level foundation features that retain much more of the fine-grained CLIP detail for each pixel (LSeg was quite coarse), and these embeddings can be queried with the efficient vector similarity metrics!

What they did differently is to first run an object segmenter, which found the top 100 objects in the image; then the whole image and each of these 100 (likely overlapping) crops gets CLIP run over them. Then each pixel has some of these 101 embeddings that are relevant to it added up. The result (below) is very impressive.

Some drawbacks that they mention is that there are millions of 3D points over an apartment-scale scene, each augmented with high-dimensionality embeddings, requiring large amounts of memory and inducing latency; so there is work to do to make this practical for mobile robotics, but the approach is elegant and transfers some of the LLM real-world reasoning magic to the 3D space.

They also mentioned concurrent work that are doing robot navigation based on language commands, might need to check out (VLMaps [44], LM-Nav [45], CoWs [46], and NLMap-Saycan [47]; numbers are the references within the ConceptFusion paper).

![concept fusion]({{ site.baseurl }}/images/survey/conceptfusion.png)

---

---

## [PITCH](#pitch)
    
### Perception Systems

*Semantic perception and mapping.*

Sensor input, semantic segmentation on the input, then 3D mapping of the semantic knowledge.

#### Sensor input

Sensors would likely be RGB-D, as they are popular in robotics and the depth knowledge allows not to use computer calculations for distance data. Though we may find out that their precision is not satisfactory. LIDAR is a depth data improvement option, but takes work to fuse the input. Computational methods (COLMAP and NeRF) are another option on improvement on the data processing side.

There is the aspect of motion blur and bad lighting on robots, which event cameras seem to be able to help. For the scope of this project we likely won't have event cameras ourselves, but will keep up with research so that we can point to where the solution for motion blur is, and at what stage of development.

#### Semantic segmentation

Semantic segmentation looks to be ConceptFusion inspired, as the elegancy of finding objects and then the fusion of multiple CLIP embeddings per pixel to keep that granular semantic knowledge is inspiring.

Potential avenues for improvement – personally had the idea to play with granularities, as the robot, while in the move to target phase, wouldn't mind larger granularities of points (definitely for semantic data; for movement it needs enough for navigation without crashes). And you could then define focus areas, likely when the robot has arrived at the location and is ready for the manipulation phase, where the granularity increases. This should reduce computational costs making the technology more appropriate for robotics applications.

Any other improvement ideas? (gunti? ingu?)

#### 3D mapping

Our team has most explored NeRFs, as that was our entry point to this type of task, but they seem needlessly costly for robotics (most definitely the rendering part; **though maybe there is some interesting way to use NeRFs, with or without the feature grid, pre-rendering for robots...**). Mapping from a static POV with a depth camera is seemingly trivial (depending on quality of sensor data of course). Mapping from a moving robot is classicaly done with the SLAM approach, which... works? maybe... our partners who have more experience with robotics seemed to insinuate in our last meeting that the 3D mapping while moving doesn't yet have any elegant solution.

Default might be a point cloud. An interesting one might be some version of NeRFs which we use in their pre-rendered state.

**gunti? ingu?**

### For the next call w Anspoks

- run mask2former (for object detection)
- run CLIP on the crops
- for presentation demo show how queries light up in different crops








[agricultural review]: https://pdf.sciencedirectassets.com/271304/1-s2.0-S0168169922X00069/1-s2.0-S0168169922004021/main.pdf?X-Amz-Security-Token=IQoJb3JpZ2luX2VjEBQaCXVzLWVhc3QtMSJHMEUCIA9KO1TnxwN5lf9G0dCxAo2MDdRYrh3CwwkL9v383GLVAiEA45btBAA0Em3PVDvh8VRvVSWoonXgIniYyXybQjZutpYqvAUIjf%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FARAFGgwwNTkwMDM1NDY4NjUiDG4Z%2F8Tu0Eow%2BzeLsCqQBa9q2haNx%2BzNcx8BgcIPLxWoSrgoiluN5%2BuN4SWZC6FNy5ZWqOtiw5vJ5uicw7MOB782tHegmMRJiJqjzesuX6v42xx%2B15dgNTZBDwOOHRW%2F607RIy1RFp%2BqaCHwm9WE2nw%2B8MhHjEkgHzZU%2BfNEiYSxpcWH8ZdPrqNIpHy6GMTAVI62B%2Fb8seh8tXNzGuZpsl9uqNTHjyr9RMmh11YFTTgRviPyh6W89GJQ%2FoC9SmsZOffOccaohdulDVQblP7YwZ3ITeJY67sJcsSiV11efJID5zcdoDHEgXeqf6J7q8DmnCrOEIh7EqzOpXLcCOWyqhTNzA6cxNbhGqBIuVIQtUMCg8TYyimM6QBAjCnIU1kYGOOGb6Tzl3fVgduM4f6sfL5C301wI8Jnix%2FmDuvrcnx59KfSfskO%2Bz2j8Nyou%2FbHwLhBcLhHpoxIDecWE60w6dyjZ7HKezHOJX%2BP%2BxUWT1VwNe6o%2FBLxodrOwT91CjPTEOE0E3tx3QOkaE6D9K9cwD0V1DWtXDtPai7MPlXOpKfivcGIgWfaMUPLdNaHq1C1ksTq7pPLNF7Ip022gp8ps2ikGHeKp5e0dVG51C3jGKqfc28PbPaHl%2Bcz8vIVEkSUgz5u8ntU8WwOPwE3TFxUmBfe56RbDJK1rG1ZP5c7HxoSqZVUTCPhT7mNgdeO6MncBFu73XeU2gPE3e7I%2BhvPXgV29qpY2fIpyBI8aywMqyuCBwDlQuxm7EBX8TugvRBy7wuBfuKCI3WAJ8Wpoh%2FzhudJNmB5eGNzLVsktCo6Ny0EJ4Qt6DFtQNG2n3paDh2Fg1Cw8VvENTzPfUrghfIYHW%2Bu1jcSuyBSf2Q87MC5Xh8xpBnNNltvTgSJ%2B2BAlR3uMLjb3p4GOrEB3FJmFLIMycGZnKb4YtE1giyeIVxofwn%2FUMrsy0ZdnJRwbYBPtEIOQlMriI5LBdFOdbhCTScLY1%2BPutRaQLj%2BZl%2BNHu2t1hxLOqorNyRPHlvDOe9%2FKbZyUScDKliXHbBQzEFK%2BMg235Yg62Ayl3AY%2FhucomDI5n12KwXlCsU0FT2XbQUlStF7YoSO2S9oIm%2F1wHpR6seMpBzEsFm8pJ7FDiGvNLFURmJdb6PoCE0kZn52&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Date=20230130T131451Z&X-Amz-SignedHeaders=host&X-Amz-Expires=300&X-Amz-Credential=ASIAQ3PHCVTYWBTLHGOI%2F20230130%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Signature=f2d02a4dc1022725ccc6013f2803180bbd6935865547d670da072becf3280529&hash=e69ebb83c652643ce1e02a4cc3e94cc73731992a1c61c8c33fabc3a8fb51e614&host=68042c943591013ac2b2430a89b270f6af2c76d8dfd086a07176afe7c76c2c61&pii=S0168169922004021&tid=spdf-8495b05b-616d-4f3f-814d-326a2305f63f&sid=0b26fb132554a7472149ed9879d7ac59fbf9gxrqb&type=client&tsoh=d3d3LnNjaWVuY2VkaXJlY3QuY29t&ua=0911515a530058595e54&rr=791a79135907b80f&cc=lv

[weeding review]: https://pdf.sciencedirectassets.com/271304/1-s2.0-S0168169922X00045/1-s2.0-S0168169922001971/main.pdf?X-Amz-Security-Token=IQoJb3JpZ2luX2VjEBQaCXVzLWVhc3QtMSJGMEQCIHpt94s%2Fjne85TSLbXMn%2FiJS3CVkvxf9j3rY62LgiwFqAiBUDi2X72zZqZh%2FdRM%2FTbnKpSpBMWkMjJMCSe%2FCV3IPvCq7BQiN%2F%2F%2F%2F%2F%2F%2F%2F%2F%2F8BEAUaDDA1OTAwMzU0Njg2NSIMSSNRnEYkI%2BZuUdnRKo8FqbX1zqvqvpyPTGvGwrvn19ylhAJXrMBENo65GcfBOncVMwrLcYdXXMut1BJ63RmI%2FBbc8Lq7%2BIFIY4FeMRzOAC%2BKnIt3bg82p3SW0he2fUSPBL%2FhjwX9wPUJPQecsgbLG4Orm2zQ%2FyxiN9mEpdY3ZnAuO1pvMN9rE%2FIsBD88HGV9tjCQw6KwXvk4Sau4tdbzodse255O3xABOhfDuGDL3%2Fa%2FEVcO2KCWH8BCLeaE2CRMbtPJ8nTS7F6iAFon2DdwCmBAwGgabMKH7KKLc8eLvdRICbuI7b52nm8u4lRtVNBvpsFKwBRvIMQY1aBhkvHE4BDoGxJnD6%2Bmdmp0VkylSERvSC0Ih73AIW2zz%2FwjDgsaRlL68pVFYrsMRS3CCmglhbB51QTkV8nNo2pOvg5PbRi4%2BfVpcdnlsSknchK86Is85RsmfUKYN1Ww4WXs5%2BYrkTsYHn9RZHXb8q8MVYiwjECIQcUCaLVydRcuutXxuAcHp%2BrJBhuKz9RLLvEe98xPEhIDLCMa889yZ25ySQDnCbaX%2BzzkZIjDZsCe9GS%2BWHGfwePUXQCFxRLNPv%2FSi%2BuHpj%2BZdYpEaL47lUeKppdSvzU6HWcYTp0%2BQ%2FZn3iM%2FpyYF2vVHbT3HctKND2A3fGL5cBpEQR%2BpuE0z6npXDfAkl%2BlToc8x%2FYGaDGRDRXmZwsJ0MJhlXqVuu7gxsibZy20oaA2Ten8m%2B31JgJ3mHQErkSLhIBTzARnTZ9taXLySsBtyXCcOcov1Th37UcPwSNJRUv%2Flj9FUqmdUmH3Cct7F%2FwoMkdFEGrDjXQgXsTJ5QIc54Y%2FJgl6UYR%2FwoBajtvvfSp863cHZmxgi4mULHFyJjjFEHjEaVnWbG2PDwxF4QzDl4t6eBjqyAaTy526p6KcdyQVc8elihEKgf%2BGn3hTDuSVHV8c9YIYJZkPibcka6M1qzrGM%2B5RJ%2F278uFCbAw0C%2BTseO%2BExLsoDcqsUeHnh2bnA0fq9pw8a%2FIKjg0Msiejp4UN2nxCgV%2Fw7y8N0Xq8QN%2FSFXt%2FmgodsdVahsPZhd6NxnSVWntUfz3gRw%2Bw9HY839lCIvogNFSZEdlumZ7BYxfDmwmSVi2R2a2Vz8ngwZs1XyAWWRA84mYU%3D&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Date=20230130T130444Z&X-Amz-SignedHeaders=host&X-Amz-Expires=300&X-Amz-Credential=ASIAQ3PHCVTYYPOCW4F6%2F20230130%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Signature=0d7978f440fb4240423a62d56f404f3500a03ed23f44b3191e840764dda1ce5a&hash=65aa51e02c057106ac5cdbbe04cdda6be5a1d939205f57dcf55b504f4bb77f1d&host=68042c943591013ac2b2430a89b270f6af2c76d8dfd086a07176afe7c76c2c61&pii=S0168169922001971&tid=spdf-c08a24d9-d0e6-4bcd-9bab-0aa68ddb3e65&sid=0b26fb132554a7472149ed9879d7ac59fbf9gxrqb&type=client&tsoh=d3d3LnNjaWVuY2VkaXJlY3QuY29t&ua=0911515a530059015b51&rr=791a6a466bf3b809&cc=lv

[geom prim]: https://pdf.sciencedirectassets.com/271576/1-s2.0-S0097849322X0006X/1-s2.0-S0097849322001224/main.pdf?X-Amz-Security-Token=IQoJb3JpZ2luX2VjEBYaCXVzLWVhc3QtMSJHMEUCIC5GFlHUFJcF1D4oPIwgz8M2E8rbMu1xX8yQgOpQ4oCRAiEA9Xc2oBA47dUe6bC3aJfpixNdv%2Bf%2Fo92aeqDtq071B3IqvAUIjv%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FARAFGgwwNTkwMDM1NDY4NjUiDAjIOvLLovyeS7BjzSqQBbSaYBK000YwoypL4HbsqGU0LSc3sKeRuUK6zWGiCGxrbKBsfhBml7lIjZEhECAHtupMe6Gzx1%2BLAtGaWbOAkKN0ph3LF%2BVAI3c%2BbgRbrYFJhkEqaKmUzmoR0FpWQRGivf7WWKdq%2B79l8r%2BH92m52T17OljIB0tKZ6pSVxNESVIsD8JBBekoVqiqBMyJjQuPBIBl4mLR7lNcwGydMwNiQr4UjJm1BWMnUDS3cM77cR57TDstTdi04EVRaqU%2BS6cqBsFilA7iwvyBf0JAJpidUiWfV6qXJS6oIqYnqJWrF%2FxVm3cqCv%2FpVcYRInWsPdrV7ZhMD%2Bab6qQiLcGUvaPrSObweSMB3nTf9Te9WQN3oKCPX4ywdl52y1rKKtaR0qwJ5zimMH%2FINQq%2BA2g6A1zKakzUv98q%2F2XLbGRzsz2hHaZ6VYhcRj7He1u48FT8CH7%2BW55FOd5OKRR0sO3DAGD8CJCL%2Ba5nq2KJGckg2auaIfwK40UIovmIB9EzSgjCj3v2BrLTeSidVpmm5TagvxCYl65xF8U9pbJPLVsEgqUwrFiw2yJztrVD4nBlGudIZT4TO94wgPxdKkrO3kiPGGvrqTOfTC2lhdsXj20OSvtupIoEFC7otyU8Wq7th92gxyJuWXFXoj8NdmkpQlCLl%2FVEvIHCn3WWU8N%2BSkgtU1ElwtX41aWd%2Fgbp36qDT7R0y5rP8LJh3VNyZaLsbcu2mWOvyCoHVhPSjUcB3G%2BSpTnJ4hS7%2FtgvwRip5DFIhA7VtWUUbyElqJkwwiBXIWMisX%2B5GYpQ%2FFqFnbmovMvIweoIs7DCC0eZJzr15kiij1HGT%2B1D4PSUPZigelYM48Rmd5F7w5zWC1lIHR24W%2BtpsVlyoPt4MIuI354GOrEBV6AdaCzbFD16UsIDMsEx7ba7yP9aRhJMOhzXCQBdfamZpoDlZhiDcpKm%2Bw7cFB743BBjv6CM7qYUZvE%2BZ2F9xfZnAeJym698TsZNtJVbXt2HOzpg11GH1J0h1v56Q9mxdAOuQI%2Bo4rY9myuaJ5QuzwUNx6HW6yqXXnmp9u8LOiOu3B8mhnEWQrevgHV%2F%2FXVmCbcQ2WWxKTTWBF7dhhi%2F4xHw83nRF2%2FGxLX1JBUoOe%2Bx&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Date=20230130T134422Z&X-Amz-SignedHeaders=host&X-Amz-Expires=300&X-Amz-Credential=ASIAQ3PHCVTYRCSWMRME%2F20230130%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Signature=917fd643a1166b46b8dfdb44ed74d75835e1a9bccba9f660d53df22267e66b54&hash=4e24ac3fa0a8c53391f62e63cbdb73671c3aff7196626f30eaa34ec47f762812&host=68042c943591013ac2b2430a89b270f6af2c76d8dfd086a07176afe7c76c2c61&pii=S0097849322001224&tid=spdf-03a404a8-e2f7-41c8-8220-7129d19bfefe&sid=0b26fb132554a7472149ed9879d7ac59fbf9gxrqb&type=client&tsoh=d3d3LnNjaWVuY2VkaXJlY3QuY29t&ua=0911515a53000e545a55&rr=791aa4523b3bb80f&cc=lv