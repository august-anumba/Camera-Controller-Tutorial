# Movement Controller Tutorial

This tutorial shows how to create a player movement controller in Unity.

## 1. Creating `PlayerMovement` C# Script

In your chosen scene and folder, create a new C# script by right clicking and selecting `Create` then `C# Script`.

![image](https://github.com/august-anumba/First-Person-Camera-Controller-Tutorial/assets/146851823/370bb5c7-007c-40b0-b1c2-0bfe649d0440)

We will be naming this new C# Script `PlayerMovement`.

## 2. Programming the X and Y Movements

We start my making a float for the `moveSpeed`, a `public Transform` for `orientation`, two more floats for `horizontalInput` and `verticalInput`, a `Vector3` for the `moveDirection` and a reference to the `Rigidbody`, we do this by writing the following lines of code, I have also added a `Header`:

```.cs 
    [Header("Movement")]
    public float moveSpeed;

    public Transform orientation;

    float horizontalInput;
    float verticalInput;

    Vector3 moveDirection;

    Rigidbody rb;
```

Now in `private void Start()` we want to assign this `Rigidbody` as well as freeze its rotation:

```.cs
 private void Start()
    {
        rb = GetComponent<Rigidbody>();
        rb.freezeRotation = true;
    }
```

Next we create an input function for the keyboard inputs like `Horizontal` and `Vertical` using the code below:

```.cs
 private void MyInput()
    {
        horizontalInput = Input.GetAxisRaw("Horizontal");
        verticalInput = Input.GetAxisRaw("Vertical");
    }
```

We also need to call MyInput in `private void Update()`:

```.cs
  void Update()
    {
        MyInput();
    }
```

We are now ready to create the `MovePlayer` function. 

```.cs
 private void MovePlayer()
    {

    }
```
We want to first calculate the movement direction by creating a new `Vector3` called `moveDirection` and setting it equal to `orientation.forward` multiplied by our `verticalInput` plus our `orientation.right` multiplied by the `horizontalInput`. This way the player will always walk in a direction relative to where they are looking.

```.cs
        moveDirection = orientation.forward * verticalInput + orientation.right * horizontalInput;
```

To now add force to the player, we can now use `rb.AddForce()` in the direction we just calcuated under `moveDirection.normalized` multiplied by the `moveSpeed` and multiplied by 10 to make the movement a little faster as well as using `ForceMode.Force`

```.cs
        rb.AddForce(moveDirection.normalized * moveSpeed * 10f, ForceMode.Force);
```

The `MovePlayer` function should look like this:

```.cs
private void MovePlayer()
    {
        moveDirection = orientation.forward * verticalInput + orientation.right * horizontalInput;

        rb.AddForce(moveDirection.normalized * moveSpeed * 10f, ForceMode.Force);
    }
```

We also need to remember to call the `MovePlayer()` function in `private void FixedUpdate()` by writing this code:

```.cs
private void FixedUpdate()
    {
        MovePlayer();
    }
```

## 3. Assigning scripts and inputing variables

We then assign this newly made C# Script `PlayerMovement` to the `Player` GameObject as a new component and set up the variables like `Move Speed` to 7 and `Orientation` to the `Orientation` GameObject

![image](https://github.com/august-anumba/Movement-Controller-Tutorial/assets/146851823/680aedb1-63f5-40c6-9867-8f090d41d51c)

## 4. Programming the drag and speed controls

If you run the game at this point you will notice that the player seems to have no friction, sliding everywhere and is as if the player is walking on ice.

To fix this we now head back into the `PlayerMovement` C# script and create a new header below the `Movement` header and and `public float moveSpeed`. This header will be titled `Ground Check` because, first we need to check if the player is on the ground and only then we want to apply drag, because appling drag mid air will feel weird to the player.

First then create a new `public float` for the player called `playerHeight` and a new `public` `LayerMask` for what is ground in addition to a `bool` called `grounded`. We also create a `public float` variable called `groundDrag`. It should look like this:

```.cs
    public float groundDrag;

    [Header("Ground Check")]
    public float playerHeight;
    public LayerMask whatIsGround;
    bool grounded;
```

To now peform the ground check we want to shoot the raycast from the player's current position down and see if it hits something the length of this ray will be half of your player's height plus a bit more. To achieve this in code we need to go to `void Update` and code the maths as seen below:

```.cs
     grounded = Physics.Raycast(transform.position, Vector3.down, playerHeight * 0.5f + 0.2, whatIsGround);

```

In addition to this we also add an `if` `else` loop as seen below to apply the calculated drag:

```.cs
if (grounded)
            rb.drag = groundDrag;
        else
            rb.drag = 0;
```

Now back in unity we create a nwe layer mask by clicking `Add Layer` and we create one called whatIsGround and apply it to the ground object:

![image](https://github.com/august-anumba/Movement-Controller-Tutorial/assets/146851823/1e6ff325-c82a-4b67-a71e-5ab005e8953c)

We now set the values of the script `PlayerMovement` in Unity like so:

![image](https://github.com/august-anumba/Movement-Controller-Tutorial/assets/146851823/c4620319-846b-4891-8ddf-de86d66ee82e)

Now running the game, the movement should now feel good and have drag, however the player still reaches speeds exceeding what was set. To fix this we add a new `SpeedControl` function to limit the players speed manually.

```.cs
private void SpeedControl()
    {
        Vector3 flatVel = new Vector3(rb.velocity.x, 0f, rb.velocity.z);

        if(flatVel.magnitude > moveSpeed)
        {
            Vector3 limitedVel = flatVel.normalized * moveSpeed;
            rb.velocity = new Vector3(limitedVel.x, rb.velocity.y, limitedVel.z);
        }
```

In the script above we are basically telling the game if the player goes faster than the movement speed it calculates what your max velocity would be and then applies it. We then call this function in `private void Update()`

The full complete `PlayerMovement` C# script should look like this:

```.cs
using System.Collections;
using System.Collections.Generic;
using UnityEditor.Experimental.GraphView;
using UnityEngine;

public class PlayerMovement : MonoBehaviour
{
    [Header("Movement")]
    public float moveSpeed;

    public float groundDrag;

    [Header("Ground Check")]
    public float playerHeight;
    public LayerMask whatIsGround;
    bool grounded;

    public Transform orientation;

    float horizontalInput;
    float verticalInput;

    Vector3 moveDirection;

    Rigidbody rb;


    private void Start()
    {
        rb = GetComponent<Rigidbody>();
        rb.freezeRotation = true;
    }

    private void Update()
    {
        grounded = Physics.Raycast(transform.position, Vector3.down, playerHeight * 0.5f + 0.2f, whatIsGround);

        MyInput();
        SpeedControl();

        if (grounded)
            rb.drag = groundDrag;
        else
            rb.drag = 0;
        
    }

    private void FixedUpdate()
    {
        MovePlayer();
    }

    private void MyInput()
    {
        horizontalInput = Input.GetAxisRaw("Horizontal");
        verticalInput = Input.GetAxisRaw("Vertical");
    }

    private void MovePlayer()
    {
        moveDirection = orientation.forward * verticalInput + orientation.right * horizontalInput;

        rb.AddForce(moveDirection.normalized * moveSpeed * 10f, ForceMode.Force);
    }

    private void SpeedControl()
    {
        Vector3 flatVel = new Vector3(rb.velocity.x, 0f, rb.velocity.z);

        if(flatVel.magnitude > moveSpeed)
        {
            Vector3 limitedVel = flatVel.normalized * moveSpeed;
            rb.velocity = new Vector3(limitedVel.x, rb.velocity.y, limitedVel.z);
        }


    }
}

```

You can now test the scene you have built yourself, or by running the demo scene provided.
