# <img src="./assets/icon-2-new.png" width="40" title="1Password"> QuasiSim: Parameterized Quasi-Physical Simulators for Dexterous Manipulations Transfer




### [Project](https://meowuu7.github.io/QuasiSim/) | [Gradio Demo](https://huggingface.co/spaces/xymeow7/quasi-physical-sims) | [Video](https://youtu.be/Pho3KisCsu4)

The implementation of the paper [**QuasiSim**](https://meowuu7.github.io/QuasiSim/), presenting a parameterized family of quasi-physical simulators for transferring **kinematics-only human manipulation** demonstrations to **dexterous robot hand simulations**. We tackle the optimization challenge posed by the complex dynamics inherent in dexterous manipulation by solving the control trajectory optimization problem through a physics curriculum.

<!-- We tackle the optimization challenge posed by the intricate dynamics involved in the dexterous manipulation via gradually solving the control trajectory optimization problem through a physics curriculum.  -->
<!-- challenge posed by the -->
<!-- The inherent difficulties of the task posed by intricate  -->
<!-- presenting a ***generalizable HOI denoising model*** designed to ***curate high-quality interaction data***. -->



https://github.com/Meowuu7/QuasiSim/assets/50799886/44233442-3382-4de8-8dbc-9e48b2b6c271

We enable accurately controlling a simulated dexterous robot hand to track complex human manipulations with **changing contact**, **non-trivial object motions**, and **intricate tool-using** in a physically realistic simulated environment. 

The repository contains 
- Analytical part of the parameterized quasi-physical simulator; 
- Detailed instructions on the optimization process for a manipulation sequence example (first two stages). 

We will add the remaining code and instructions on the last optimization stage, as well as the data and more manipulation examples. 
<!-- We will add the data and the evaluation process for the remaining test datasets, as well as the training procedure. These updates are expected to be completed before May 2024. -->


## Getting Started


This code was tested on `Ubuntu 20.04.5 LTS` and requires:

* Python 3.8.8
* conda3 or miniconda3
* CUDA capable GPU (one is enough)


### Environment setup

<!-- TODO: environment setup -->




<!-- (important Diffhand setup)  -->

1. Creat a virtual environment and install necessary dependencies

Create a virtual environment

```shell
conda create -n quasisim python==3.8.8
conda activate quasisim
```

2. Install `torch2.2.0+cu121`
```shell
conda install pytorch==2.2.0 torchvision==0.17.0 torchaudio==2.2.0 pytorch-cuda=12.1 -c pytorch -c nvidia
```


3. Install `torch_cluster`:
```shell
mkdir whls
cd whls
wget https://data.pyg.org/whl/torch-2.2.0%2Bcu121/torch_cluster-1.6.3%2Bpt22cu121-cp38-cp38-linux_x86_64.whl
pip install torch_cluster-1.6.3+pt22cu121-cp38-cp38-linux_x86_64.whl
```

4. Install other dependences

```shell
pip install -r requirements.txt
```


5. Setup [DiffHand](https://github.com/eanswer/DiffHand):
```shell
cd DiffHand
cd core
python setup.py install
### For testing the installation ###
cd ..
cd examples
python test_redmax.py
```
It's better to install it from this project since we have made modifications to support our purpose.


### Get data 

**Examples data**

<!-- TODO: examples data downloading (those you put in the data folder) -->
Download the [example data](https://1drv.ms/f/s!AgSPtac7QUbHgVE5vMBOAUPzxxsV?e=B5V6mo) and organize them in the same way as we do in OnDrive. The expected file structure is as follows: 
```shell
data
 |-- grab
     |-- 102
         |-- 102_obj.npy
         |-- 102_obj.obj
         |-- 102_sv_dict.npy
         |-- 102_sv_dict_st_0_ed_108.npy
     ...
```



**Checkpoints**

<!-- TODO: checkpoints downloading -->
Download [pre-optimized checkpoints](https://1drv.ms/f/s!AgSPtac7QUbHgUNU7vF8a7V0WS9t?e=lp5GSU) and organize them in the same way as we do in OnDrive. The expected file structure is as follows: 
```shell
ckpts
 |-- grab
     |-- 102
         |-- xxx1.pt
         |-- xxx2.pt
     ...
```


**URDFs and others**

<!-- TODO: miscs data downloading -->


Download [rsc.zip](https://1drv.ms/u/s!AgSPtac7QUbHgUKUL6O4E7_0ygNT?e=PZlb0I) and [raw_data.zip](https://1drv.ms/u/s!AgSPtac7QUbHgUEIHkPdmjmHUMQc?e=Q9IYY1). Extract them in the root folder of the project. 

*Important:* Download [mano_pts.zip](https://1drv.ms/u/s!AgSPtac7QUbHgV_qhMSUm-Vmepk9?e=3ObaVb). Extract them in the folder `rsc/mano`. 



## Example Usage

<!-- In the current stage, this repo mainly contains source code on the analytical p -->
<!-- We include data, detailed instructions, and result of an example, aiming at present the inverse dynamics problem in the contact-rich manipulation scenario that we can leverage QuasiSim to solve and the optimization process. Currently we release the analytical part of QuasiSim and the first and second optimization stages. The thrid stage along with more examples will be added. 

For the example sequence `data/grab/102` showing a human hand rotating a mouse, the human manipulaton demonstration, tranferred manipulation to the simulated Shadow hand in the QuasiSim's analytical environment, and the manipulation optimized in the Bullet simulator are shown as follows.  -->

We provide the data, detailed instructions, and the results of a specific example with the aim of elucidating the inverse dynamics problem within contact-rich manipulation scenarios, which we address leveraging the physics curriculum programmed from QuasiSim. Currently, we release the analytical part of QuasiSim as well as the first two optimization stages. The thrid stage, alongside with more data and their optimization code will be added. 
<!-- we are unveiling the analytical component of QuasiSim, as well as the initial and secondary optimization stages. The third stage, along with additional examples, will be forthcoming. -->

In the case of the example sequence `data/grab/102`, which depicts a human hand rotating a mouse, the human manipulation demonstration, transferred manipulation to the simulated Shadow hand in the QuasiSim's analytical environment, and the manipulation optimized in the Bullet simulator are shown as follows. 
<!-- we present the following progression: demonstration of human manipulation, transfer of manipulation to the simulated Shadow hand within QuasiSim's analytical environment, and optimization of manipulation within the Bullet simulator. -->


|        Human Manipulation        |       Transferred to Shadow         |         Transferred to Shadow in Bullet         |
| :----------------------: | :---------------------: | :-----------------------: |
| ![](assets/human-1.gif) | ![](assets/robo-1.gif) | ![](assets/robo-bullet-1.gif) |

<!-- The following instructions aims at optimizing for the control trajectory that can drive  the Shadow hand to complete the tracking task (as shown in the middle demo) in the stiffest analytical environment of QuasiSim.  -->

The following instructions aim to optimize the control trajectory to enable the Shadow hand to complete the tracking task, as demonstrated in the middle demo, within the stiffest analytical environment of QuasiSim.

### Stage 1
> **Transferring human demonstrations via point set dynamics.** In this stage, we represent the MANO model and the Shadow model within the simulation as parameterized point sets. The contact model is adjusted to its softest level. The objective is to optimize a trajectory of point sets for the Shadow hand to successfully accomplish manipulation tracking.

This stage is divided into four steps as described below.

**Step 1: Optimizing the trajectory of the simulated dynamic MANO hand**

In this step, we optimize a control trajectory for the dynamic MANO hand model to track the reference manipulation. Please execute the following commands sequentially:

```shell
bash scripts_new/train_grab_mano.sh
bash scripts_new/train_grab_mano_wreact.sh
bash scripts_new/train_grab_mano_wreact_optacts.sh
```

**Step 2: Optimizing the control trajectory for the point set constructed from the MANO hand** 

Execute the following four commands sequentially for this step:

```shell
bash scripts_new/train_grab_pointset_points_dyn_s1.sh
bash scripts_new/train_grab_pointset_points_dyn_s2.sh
bash scripts_new/train_grab_pointset_points_dyn_s3.sh
bash scripts_new/train_grab_pointset_points_dyn_s4.sh
```

**Step 3: Optimizing the trajectory of the kinematic Shadow hand**

<!-- In this step, we optimize for a kinematic Shadow hand trajectory based on the keypoint based correspondences and mesh surface point based correspondences. Execute the following command for this step:  -->

In this step, we optimize the trajectory of the kinematic Shadow hand based on keypoint-based correspondences and mesh surface point-based correspondences. Execute the following command for this step:

```shell
bash scripts_new/train_grab_sparse_retar.sh
```

**Step 4: Optimizing the control trajectory for the point set constructed from the simulated Shadow hand** 

Please execute the following commands for this step:

```shell
bash scripts_new/train_grab_pointset_points_dyn_retar.sh
bash scripts_new/train_grab_pointset_points_dyn_retar_pts.sh
```





### Stage 2
> **Tracking via a contact curriculum.**  In this stage, we optimize the control trajectory of the simulated Shadow hand to complete the tracking task through a curriculum of contact models. Initially, the contact model is tuned to the softest level. Subsequently, we gradually adjust parameters to tune it to the stiffest level.

Run the following command: 
```shell
bash scripts_new/train_grab_stage_2_dm_curriculum.sh
```


<!-- ### Stage 3 -->


## TODOs

- [x] Analytical part of QuasiSim
- [x] Optimization example
- [ ] More examples
- [ ] Full version of QuasiSim
- [ ] Custimizing your optimization



## Contact

Please contact xymeow7@gmail.com or raise a github issue if you have any questions.


<!-- ## Bibtex
If you find this code useful in your research, please cite:

```bibtex
@inproceedings{liu2024geneoh,
   title={GeneOH Diffusion: Towards Generalizable Hand-Object Interaction Denoising via Denoising Diffusion},
   author={Liu, Xueyi and Yi, Li},
   booktitle={The Twelfth International Conference on Learning Representations},
   year={2024}
}
``` -->


## Acknowledgments

This code is standing on the shoulders of giants. We want to thank the following contributors
that our code is based on: [DiffHand](https://github.com/eanswer/DiffHand) and [NeuS](https://github.com/Totoro97/NeuS).

## License
This code is distributed under an [MIT LICENSE](LICENSE).

