# Shooting & Damaging Package

This package will make your weapons fire when there's ammo, stop when there is none left and reload when the weapon is empty.

## Writing the script

After opening the Unity project create 2 C# script files and name them appropriately, such as **Gun** and **NPCHealthSystem**. One will be used for the gun's shooting and damage output, and the other will be used to manage the ammo of the gun.

### Gun Script

When you open the Gun script, make sure that you delete the **using System.Collections.Generic** but keep the **using Unity.Engine** and **using System.Collections** as the script  will need a Coroutine function and with out the **using System.Collections**, no Coroutines can be used.

First create two public float variables called **damage** and **range** and give them any number you want. Next, create a public Camera variable and name it appropriatelyas this will be used to make the gun shoot though the camera, **not the gun**. Under the functions, create a private void function called **Shoot** (or any name you find more appropriate) and write the following in it:
    
    void Shoot()
    {
        currentAmmo--;

        RaycastHit hit;
        if (Physics.Raycast(fpsCam.transform.position, fpsCam.transform.forward, out hit, range))
        {
            Debug.Log(hit.transform.name);
        }
    }
            
The debug method is going to be used temporarily so we know what gameobject we hit that's containing a collider.

To make the gun fire, go to the Update method and call the Shoot function by writing the following:

    if (Input.GetButtonDown("Fire1"))
        {
           Shoot();
        }

If you did everything accordingly, you should now see that whenever you shoot and the raycast hits something, it should display the name of the gameobject in the console tab. If you don't see anything, it might be because that object does not contain a collider.

To make it easier to tell where the gun is aiming at, create a UI Image for the crosshair and use any image you like, as well as any colour you like. After creating the image, reduce the size of it to 10 width x 10 height as the gun will be firing via a small radius through the camera. After resizing the crosshair, make sure that it is centered on the canvas. The image will always be centered by default, but if you want to be sure, click on the image and in the Rect Transform component, click on the box, hold Alt and select the centre.

By now, the crosshair image should now be centred on the canvas and wherever the crosshair is pointing at, the gun should be displaying the name of the object in the console and everything is working fine.

Next step is to make the gun script deal damage. To do this, write the following extra bits of code in the Shoot function:

    void Shoot()
    {
        RaycastHit hit;
        if (Physics.Raycast(fpsCam.transform.position, fpsCam.transform.forward, out hit, range))
        {
            NPCHealthSystem target = hit.transform.GetComponent<NPCHealthSystem>();
            if(target != null)
            {
                target.TakeDamage(damage);
            }
        }
    }

The **NPCHealthSystem** script is what this script will be using to make sure that whatever the raycast hits containing that script will take damage, as these two scripts will be working together to make sure that whatever damage the gun does in the Gun script, it will reduce the health in the NPCHealthSystem script (explained further on).

At this point, the debug script can go as it is no longer needed if you managed to get the code to call out the names of the gameobjects the raycasts hit when shooting the gun.

### NPCHealthSystem



## How The Two Scripts Will Work Together



## What Else Cen Be Added to The Script



### Reload (with Animations)



### Limiting Ammo



### Adding Effects To The Gun (Not Included in Package)

If you want to add some particle effects for the gun, such as muzzle flashing, create a public ParticleSystem variable and name it **muzzleFlash** (or another name you find more applicable).

## Things To Note
