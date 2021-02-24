# Raycast Gun & Damage Package

This package will make your weapons fire when there's ammo, stop when there is none left and reload when the weapon is empty.

## Writing the script

After opening the Unity project create 2 C# script files and name them appropriately, such as **Gun** and **NPCHealthSystem**. One will be used for the gun's shooting and damage output, and the other will be used to manage the ammo of the gun.

### Gun Script

When you open the Gun script, make sure that you delete the **using System.Collections.Generic** but keep the **using Unity.Engine** and **using System.Collections** as the script  will need a Coroutine function and with out the **using System.Collections**, no Coroutines can be used.

First create two public float variables called **damage** and **range** and give them any number you want. Next, create a public Camera variable and name it **fpsCam** (or another appropriate name) as this will be used to make the gun shoot though the camera, **not the gun**. Under the functions, create a private void function called **Shoot** (or any name you find more appropriate) and write the following in it:
    
    void Shoot()
    {
        currentAmmo--;

        RaycastHit hit;
        if (Physics.Raycast(fpsCam.transform.position, fpsCam.transform.forward, out hit, range))
        {
            Debug.Log(hit.transform.name);
        }
    }
            
After writing this, go to te game object that your will be using for your gun and assign this script to it. Then click and drag the camera component that you are using for the shooting into the **fpsCam slot in the Gun script** component. The debug method is going to be used temporarily so we know what gameobject we hit that's containing a collider.

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

In this script, you don't need to write much as it is just to make sure the NPC dies and does not need to include any AI behaviour.

When you open this script, delete the start and update functions, and create a public float variable, call it health and give it a number you feel is appropriate. Next, create a public void and call it **TakeDamage**. This void function needs to be public because if it is not, it can't be referenced in the Gun script and it **needs** that function to deal damage.

In the TakeDamage function make sure the following is included:

    public void TakeDamage(float damageAmount)
    {
        health -= damageAmount;
        if(health <= 0f)
        {
            Die();
        }
    }

You will notice that there is a private function called **Die**. This function will be used to destroy the gameobject when it's health reaches 0 or goes under it. So under the TakeDamage function, create the Die function and simply write the following:

    void Die()
    {
        Destroy(gameObject);
    }

This is all that is needed to make sure that when the NPC's health reaches 0, it disappears.

If you have done everything correctly, you should now see the gameobject with the NPCHealthSystem script disappears when it's health reaches 0. If the gameobject is not disappearing and you are encountering an error in the Gun script, it is possible that you forgot to set the TakeDamage finction in the NPCHealthSystem sctipt to public.

## How The Two Scripts Will Work Together

The way these two scripts work together is mainly through the damage and health aspects. You see, in the NPCHealthSystem script you set up a health variable for the NPC and created a public void function that handles the damage the NPC recieves. In the Gun script, you set up the damage output for the player's gun and you referenced the public TakeDamage function.

In other words, the Gun script is calling the NPCHealthSystem script and tells it to reduce its health whenever the raycast from the Gun script hits an object with the NPCHealthSyste script.

## What Else Cen Be Added to The Script

These are additional things you can add to the script to make it more complex.

### Reloading Gun

To start off create three vriables: a public int variable called **maxAmmo** and give it a value of your choosing, a private int called **currentAmmo** and a public float variable called **reloadTime** with a value of 1f. Next thing to do is to write the following in the **Start** function:

    currentAmmo = maxAmmo;

If you deleted the Start function go ahead and write it back in.

Next, go to the **Shoot** method and write the following:

    currentAmmo--;

This will subtract the currentAmmo value by one and this can be written anywhere i the Shoot method, but outside of an if statement is recomended.

By now, everytime you shoot, the currentAmmo value should shrink by one every time you shoot the gun. If it didn't, you might have put it in an if stetement and you made it so the bullets only shrink when another condition is met.

To make sure that whenever the gun runs out of ammo and reloads when it does, write the following in the **Update** method:

    if(currantAmmo <=0)
    {
        Reload();
        return;
    }

Now create a **private** function called reload and write the following:

    Reload()
    {
        Debug.Log("Reloading"); //So whenever the gun reloads, you know get notified on in the console tab that it worked.
        currentAmmo = maxAmmo;
    }

By now, everytime you fire the gun and it runs out of ammo, it should reload. Though there is a slight problem with the reload script: it reloads instantly and that's not going to be in synch for the reload animations.

To add a delay to this, make sure that you did **not** remove **using System.Collections** and in the Reload function, change the void to an **IEnumerator** and write **yield return new WaitForSeconds(reloadTime);** on top of **currentAmmo = maxAmmo;**. By now, reload method should look like this:

    IEnumerator Reload()
    {
        yield return new WaitForSeconds(reloadTime);
        currentAmmo = maxAmmo;
    }

Now, go back to the Update method and change the referenced Shoot method to the folowing:

    if(currantAmmo <=0)
    {
        StartCoroutine(Reload());
        return;
    }

Now whenever the ammo reaches 0, the gun will reload and there will be a few seconds (depending on reloadTime value) before the gun can fire again. After the delay, you can continue firing. However, every frame when you run out of ammo, the script will start the reload Coroutine and t should only be doing it once.

To fix this, create a privte bool, name it isReloading and set it to false. Then in the top of the Update function write the following:

    if(isReloading)
    {
        return;
    }

Now, go to the top of the Reload function and write isReloading is true and do the same at the bottom, but set the bottom one to false. The Reload function should look like this after you're done:

    IEnumerator Reload()
    {
        isReloading = true
        
        yield return new WaitForSeconds(reloadTime);
        currentAmmo = maxAmmo;
        isReloading = false;
    }

If you've written all this correctly, the gun should now reload when the gun is empty and not continue shooting whie the delay is still active.

There is one more major issue with this script: whenever the gun is reloading and the player does something, such as switching weapons and goes back to the gun that was reloading, the isReloading bool is stuck on true which means that you can no longer shoot the gun. This is because the script thinks that it hasn't finished reloading, but in truth, it never does as it's stuck.

To fix this 

### Reload Animations (Included in Package)

If you want to add some animations tht will make the reloads look more satisfying, go to **Window > Animation** to open the Animation window, click on create and a new window should pop up. If you don't have a window for the animations, create one by right clicking and selecting **New > Folder**. When you have found the folder that you feel will help you find the animations, open that folder and save the animation with an appropriate name for the **Weapon Idle**. Now, go to the drop down which currently contains the name of the animation you made, click on it and select **Create New Clip** for the **Weapon Reload**.

For a simple reload animation, simply click on the gameobject that you wis to be animated for the Weapon Reload animation, click on Record (Red circle) and simply change the rotation axis on the first frame of that game object to your prefered location. Then stop recording. For the Weapon Idle animation, just do the same but keep the first frame of the axis to 0 as this is where the gun will retutn to when the animation is done. **You do not need to move it back manualy**.

Now to set the movements. First, click on the animation controller for that gun and click on the **Animator** tab (Animation and Animator are different). If you don't have the animator tab, go to **Window > Animator** and the tab should appear.

In the Animator window, you should see the two animations you made with the same names you gave them. If you see Entry linked to the Weapon Reload animation, change it to the Weapon Idle animation by right-clickin on it and selecting **Set as Layer Default State**.

To make the transitions, right-click on the Weapon Idle animation and select **Make Transition**, then left-click on the Weapon Reload animation. Do the same to the other way around as well so it goes back and forth. By now, there should be two lines going back and forth between the Weapon Idle and Weapon Reload animations.

Right now, there are no conditions set to either of the transitions. So they'll start immediately. To make the animations start when reloading, click on parameters at the top left corner of the Animator window and click on the plus sign (+), add a new bool parameter and name it as **Reloading**. This will make sure that whenever the Reload method in the Gun script can call this parameter and make the animations play when does get called.

Next, click on the line that goes from the Weapon Idle animtion to the Weapon Reload animation and click on the plus sign under conditions. Immediately after click in on the plus sign, you should see the name of the bool you made earlier with the values True and False in the drop down bar next to it. Set that bool to true. Do the exact same thing the other way around, but set the bool to false and turn off the **Has Exit Time** for both of them so you don't wait for the animation to play out.

If you did this correctly, whenever you play the game in the project and change the bools from true and false, you should see the animations playing back and forth everytime you change them.

Now to call these animations through the script.

In the Gun script create a new public Animator variable and name it as animator (in lower case). Then go to the Reload method and write **animator.SetBool("Reloading", true);** at the top of WaitForSeconds and **animator.SetBool("Reloading", fales);** at the bottom of it. By then the Reload method should look like this:

    IEnumerator Reload()
    {
        isReloading = true
        
        animator.SetBool("Reloading", Truw);
        
        yield return new WaitForSeconds(reloadTime);
        
        animator.SetBool("Reloading", fales);
        
        currentAmmo = maxAmmo;
        isReloading = false;
    }

Make sure that the name of the Animator parameter you set in the Reload method's parentheses is the exact same name as the one in the Animator Window, because if you spelt it wrong, it will not work.

Now go back to the gun containing the Gun script and you should see a new Animator slot. All you need to do now is to click and drag the animator component from that gun in to that slot. The animator component is set to the game object you animated automatically, so you don't need to worry about forgetting so add that component to the gun.

If you've done this correctly, you should now see the animations changing whenever the gun is reloading and when it finishes. If you do not see this, you might have misspelt the name of the Parameter you set in the in the Animator.

Now there is just one last problem to deal with: as soon as the animation finishes, the gun can start shooting, even when the animation hasn't finished yet. You can fix this very quickly.

In the Reload method

### Rate of Fire



### Adding Effects To The Gun (Not Included in Package)

If you want to add some particle effects for the gun, such as muzzle flashing, create a public ParticleSystem variable and name it **muzzleFlash** (or another name you find more applicable).

## Things To Note

These script does **not** contain any movement whatsoever as this is mainly for dealing damage to something and making sure that the gameobject with the NPCHealthSystem script dies when it's health reaches 0. If you've already made a scipt that handles movement and you just want to add something that deals damage, you can simply apply these scripts to the appropriate gameobjects and re-write the values that you find appropriate.
