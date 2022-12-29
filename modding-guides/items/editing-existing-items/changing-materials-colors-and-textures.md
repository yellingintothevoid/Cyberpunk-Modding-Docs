---
description: Changing how an item looks in-game
---

# Changing materials, colors and textures

## Summary <a href="#summary" id="summary"></a>

**Created by @manavortex**\
**Published November 05 2022**

This guide will teach you how to change an item's appearance by editing its MultilayerSetup (\*.mlsetup file), how to rename materials, and how add your own mlsetups (**custompathing**).

It uses the following versions:

* WolvenKit: [8.7.1-nightly.2022-11-06](https://github.com/WolvenKit/WolvenKit/compare/8.7.1-nightly.2022-11-05...8.7.1-nightly.2022-11-06) (but anything > 8.7 will do)
* [MLSetupBuilder](../../../modding-know-how/modding-tools/mlsetup-builder.md): [1.6.5](https://github.com/Neurolinked/MlsetupBuilder) (older versions won't be compatible with WKit 8.7 and game version 1.6)

{% hint style="info" %}
In general, an item's appearance is determined by a [mlsetup](../../../developers/shaders/multilayered.md#what-is-the-mlsetup) file containing several material layers.&#x20;

Which of these layers affects which part of your mesh will be determined in the corresponding [mlmask](../../../developers/shaders/multilayered.md#what-is-the-mlmask) file.
{% endhint %}

## **Step 1: Finding your item**

{% hint style="info" %}
This tutorial assumes that you already know which mesh and appearance you want to change. If you don't know that, you need to [find the correct game file](replace-a-player-item-with-an-npc-item.md#summary). If you only have a cheat code, see [Spawn Codes](../../../modding-know-how/references-lists-and-overviews/equipment/spawn-codes-baseids-hashes.md#the-.app) instead.
{% endhint %}

We will use the female variant of the puffy vest (as I've already [mapped and documented it](../../../modding-know-how/references-lists-and-overviews/equipment/variants-and-appearances.md#reinforced-puffer-vest-4-variants)):

```
base\characters\garment\player_equipment\torso\t2_002_vest__puffy\t2_002_pwa_vest__puffy.mesh
```

{% hint style="success" %}
Add the item to your project and open it in WolvenKit. You want the original to look up material names, even if you overwrite it with your own mesh.
{% endhint %}

## Step 2: Finding the correct appearance

We will change the appearance bwstripes, which is used by Vest\_17\_basic\_01:

<figure><img src="../../../.gitbook/assets/image (25).png" alt=""><figcaption><p>find material bwstripes and remember the name of the chunkMaterial</p></figcaption></figure>

{% hint style="info" %}
`chunkMaterials` corresponds to the `chunkMasks` (submeshes).&#x20;
{% endhint %}

{% hint style="info" %}
`default` is the fallback appearance that'll be used if anything can't be resolved by name or index. This is the reason why most item swap mods give you only a single appearance - people didn't set up the [variants](replace-a-player-item-with-an-npc-item.md).
{% endhint %}

This vest has only one chunkMask, so there's only one material. Remember its name and find it in the `localMaterialBuffer`:

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption><p>It's ml_t2_002_ma_vest__puffy_bwstripes</p></figcaption></figure>

{% hint style="warning" %}
Most meshes have their materials under `localMaterialBuffer/materials`. However, some of them (especially those with physics) use `preloadLocalMaterialInstances` instead.
{% endhint %}

You will (hopefully) see a material with three entries in `values` (order doesn't matter):

| Key             | Value (DepotPath)                                                                                                     |
| --------------- | --------------------------------------------------------------------------------------------------------------------- |
| MultilayerSetup | `base\characters\garment\citizen_casual\torso\t2_002_vest__puffy\textures\ml_t2_002_ma_vest__puffy_bwstripes.mlsetup` |
| MultilayerMask  | `base\characters\garment\citizen_casual\torso\t2_002_vest__puffy\textures\ml_t2_002_ma_vest__puffy_default.mlmask`    |
| GlobalNormal    | `base\characters\garment\citizen_casual\torso\t2_002_vest__puffy\textures\t2_002_ma_vest__puffy_n01.xbm`              |

<figure><img src="../../../.gitbook/assets/image (14) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Most materials in Cyberpunk use the `engine\materials\multilayered.mt` material and assign colours via an .mlsetup file. If you're used to textures, you are probably going to hate this. As somebody who has been where you are: **the mlsetup system is cool**. Genuinely. Give it a chance!
{% endhint %}

#### multilayered.mt exporting the .mlsetup

We change the appearance by editing the **MultilayerSetup**:

1. Find the file and add it to your project.
2. Right-click the file and select "Convert to JSON".

{% hint style="info" %}
If you have set configured MLSB, you can make use of MlSetupBuilder's export feature, rather than doing it via WolvenKit:
{% endhint %}

Move your new json file in the same folder as the multilayer setup.&#x20;

<figure><img src="../../../.gitbook/assets/image (22) (1).png" alt=""><figcaption></figcaption></figure>



{% hint style="info" %}
The json file will be named `ml_t2_002_ma_vest__puffy_bwstripes.mlsetup.json` (`originalFileName.originalExtension.json`)&#x20;
{% endhint %}

#### Other materials: Textured

The most commonly used material for anything textured is `engine\materials\metal_base.remt`. Despite its name, this material isn't necessarily metallic.

<figure><img src="../../../.gitbook/assets/texture_guide_metal_remt.png" alt=""><figcaption><p>Find the BaseColor texture, and add it to your project. Then export it to *.png via the WolvenKit exporter</p></figcaption></figure>

{% hint style="info" %}
The .xbm is a container around the texture. Export the xbm to png via WolvenKit.
{% endhint %}

{% hint style="warning" %}
When re-importing textures, make sure that you use the correct `isGamma` setting:

normals: **false**\
diffuse/albedo: **true**\
anything that is used in .inkatlas files: **true**

****\
****If your texture has brightness issues in-game, toggle around the isGamma flag during import.
{% endhint %}

## Step 3: Editing the .mlsetup file

Open up MlSetupBuilder and load your .mlsetup.json file.

{% hint style="info" %}
If you select WolvenKit's "Open in File Explorer" option, you can copy the path from the explorer's address bar and paste it into the MlSetupBuilder's address bar.
{% endhint %}

<figure><img src="../../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

If you want to see which layers correspond to which part of the mesh, you can load it from the library:

<figure><img src="https://i.imgur.com/nNmwlBD.png" alt=""><figcaption><p>Optional: Find your mesh in the library</p></figcaption></figure>

{% hint style="info" %}
This step requires the tool to be [set up correctly](../../../modding-know-how/modding-tools/mlsetup-builder.md), which is not part of this guide. Fortunately, it's also optional.
{% endhint %}

Change the colours and materials to whatever you want.

{% hint style="success" %}
TBD: Create/link to material description
{% endhint %}

Save the file and overwrite the original `.mlsetup.json`:

![](<../../../.gitbook/assets/image (11) (1).png>)

If you have configured MLSB and had both files in the same folders, you will see a notification when the MlSetupBuilder has overwritten your original `*.mlsetup`. This takes a few seconds.

Otherwise, you need to right-click on the json file under "raw" and select "import from JSON".

{% hint style="success" %}
This is already working. You can pack the project and see it work!
{% endhint %}

<figure><img src="../../../.gitbook/assets/image (1) (3).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Since you haven't changed anything in the mesh itself, you can (and should) delete it from your mod. Only keep it if you want to do the steps below.
{% endhint %}

## Step 4 (optional): Custompathing

If you want to put up your own .mlsetup, rather than overwriting the original one, you can do that. All you have to do is changing the DepotPaths to the relative path of your mlsetup.

<figure><img src="../../../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
Keep your folder and file names unique! If you have two mods adding a file at the same location, the second one **will be unable to overwrite it** and will use the first mod's file.
{% endhint %}

## Step 5 (optional): Renaming materials

You can rename a material by changing the "name" property inside the CMeshMaterialEntry in the `materials` array:&#x20;

<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
Don't forget to look through all the appearances and change the `chunkMaterial` names!
{% endhint %}

## Step 6 (optional): adding new materials

To add a new material to a mesh, you need to create **two** entries. The first of those needs to be in the **materialEntries** array:

<figure><img src="../../../.gitbook/assets/item_appearance_add_name_step_1.png" alt=""><figcaption><p>Edit the new material's index and name. This is crucial!</p></figcaption></figure>

Now, add an entry in the localMaterialBuffer.

{% hint style="info" %}
If your mesh doesn#t have entries under `localMaterialBuffer`, use `preloadLocalMaterialInstances`  instead.
{% endhint %}

<figure><img src="../../../.gitbook/assets/editing_material_adding_entry (2).png" alt=""><figcaption><p>The new material will have the name you defined in the CMeshMaterialEntry in the previous step.</p></figcaption></figure>

You can now use your new material just like the regular, old materials.