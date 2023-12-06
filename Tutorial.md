# Movement Controller Tutorial

This tutorial shows how to create a player movement controller in Unity.

## 1. Creating `PlayerMovement` C# Script

In your chosen scene and folder, create a new C# script by right clicking and selecting `Create` then `C# Script`.

![image](https://github.com/august-anumba/First-Person-Camera-Controller-Tutorial/assets/146851823/370bb5c7-007c-40b0-b1c2-0bfe649d0440)

We will be naming this new C# Script `PlayerMovement`.

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
We are now ready to create the `MovePlayer` function, we want to first calculate the movement direction



---------------------
You can now test the scene you have built yourself, or by running the demo scene provided.

```.cs

```
