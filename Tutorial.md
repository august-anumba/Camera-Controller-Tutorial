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

...

You can now test the scene you have built yourself, or by running the demo scene provided.
