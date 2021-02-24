# Ammo Management Package
This package will make your weapons fire when there's ammo, stop when there is none left and reload when the weapon is empty.

## Writing the script

After opening the Unity project create 2 C# script files and name them appropriately, such as **Gun** and **AmmoManagement**. One will be used for the gun's shooting and damage output, and the other will be used to manage the ammo of the gun.

### Gun Script

When you open the Gun script, make sure that you delete the **using System.Collections.Generic** but keep the **using Unity.Engine** and **using System.Collections** as the script  will need a Coroutine function and with out the **using System.Collections**, no Coroutines can be used.

First create two public float variables called **damage** and **range** and give them any number you want. Next, create a public Camera variable and name it appropriatelyas this will be used to make the gun shoot though the camera, **not the gun**. Under the functions, create a private void function called **Shoot** (or any name you find more appropriate) and write the following in it:

    RaycastHit hit;
    if (Physics.Raycast(fpsCam.transform.position, fpsCam.transform.forward, out hit, range))
        {
            Debug.Log(hit.transform.name);
        }
        
The debug method is going to be used temporarily so we know what gameobject we hit that's containing a collider.

To make the gun fire, go to the Update method and call the Shoot function by writing the following:

    if (Input.GetButtonDown("Fire1"))
        {
           Shoot();
        }

If you did everything accordingly, you should now see that whenever you shoot and the raycast hits something, it should display the name of the gameobject in the console tab. If you don't see anything, it might be because that object does not contain a collider.

To make it easier to tell where the gun is aiming at, create a UI Image and use any image you like. After creating the image, reduce the size of it to 10 width x 10 height as the gun will be firing via a small radius through the camera.



### AmmoManagement Script



### How The Two Scripts Work Together



## What Else Cen Be Added to The Script

### Adding Effects To The Gun (None Included in Package) 

### Limiting Ammo (Optional)

## Things To Note
