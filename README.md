# floppybird-sprint
Create a flappy bird clone using Unity, scripting in C#.

## First steps
  - The first thing you will need to get started on this sprint is a free Unity account. Navigate to the user icon in the top right   corner, click "create account".
  - Now that you have that created, go ahead and download the most current version of the Unity [Personal edition software](https://store.unity.com/download?ref=personal)
  
  
  Now that you have done those things you can go ahead and fork this repo, and start working from your forked copy.
  
#### *NOTES*
Keep in mind you will not be building any textures, game art, or assets for this project. You are more than welcome to if you desire, but the ultimate goal of this sprint is to use the already loaded assets that I've provided ([via the Unity assets store](https://www.assetstore.unity3d.com/en/)) and learn the fundementals of scripting in C# with unity.


You will need to add a line to your ```.gitignore``` that looks like this: ```floppyBird/Temp/UnityLockfile```


Feel free to reference the solution branch at any time, which has the scripts file already written.

## Preview
The end goal of this sprint is to create flappy bird clone that looks like this...

![Preview](https://bit.ly/3alEVse)
  

## Adding the game objects
It's important to take a second to understand how unity is structured. Basically everything in a game built with Unity is composed of *Game Objects*. You can attach components to these game objects, and make them do special things. Components can be anything from Scripts, Audio sources, Animations, Physics, etc. Here is an okay visualization of this model.

![alt-text](https://koenig-media.raywenderlich.com/uploads/2011/08/unity14_diagram.png)

If the scripts of a game are the functionality or the brain behind how everything works, then the game objects are the rest of the body that is ultimately responsible for making your game interactive. Another the way to think of this relationship is like the MVC relationships you see of front end frameworks in web development. The game objects are most closely related to the view, the components have their equivalent counter-part called... *components*, and the model is the single source of truth embedded in the game scripts.

This step has already been done for you. The game objects are already properly positioned, configured and have the proper components attached to them. These steps are fairly simple and understanding the basics of configuring game objects and components can be easily aquired by following a few of the [Unity tutorials.](https://unity3d.com/learn/tutorials) However for the sake of brevity I've extrapolated all these complexities away for you so that we can focus on the coding portion of creating a game.

## Scripts
Basically every game object needs a corresponding script, and for arbitrary objects that don't carry enough weight to warrant their own script then we have a ```GameController``` which acts a sort of index if you will. With that said these are the following scripts that we will be writing.


- Bird.cs
- Column.cs
- ColumnPools.cs
- GameController.cs
- RepeatingBackground.cs
- ScrollingObject.cs

**You will need to add a folder titled ```Scripts``` and place it inside of your ```Assets``` folder.**

**Side Note** - *The instructions below are going to use code-blocks ```like this``` when you see this, that means that it is a function built into Unity that you can look up on the docs. I am trying to write this guide in such a way where you will be able to look up these methods on the docs and figure out how to peice the code toghether. This is for those of you that are attempting to do this sprint while not following along with a live session.*


### Bird.cs
**Dependencies** - `System.Collections, System.Collections.Generic, UnityEngine`

- This script is going to control our birds upward movement, and animations
- Need to initialize an upward velocity *float*, a *bool* that will determine whether or not our bird is alive, and references to our `Rigidbody2D` component, and `Animator`
- Need three *void functions* `Start`, `Update`, and a function to determine when our bird collides with another rigidbody
- Inside our start function initialize the reference to our `Rigidbody2D` and `Animator` using `GetComponent`
- Inside our update function we need to check if our death bool is not true, if we are indeed alive, then check if our mouse button has been clicked with `Input.GetMouseButtonDown`, if so apply upward velocity to our bird with `AddForce` and `SetTrigger` our animation.


### Column.cs
**Dependencies** - `System.Collections, UnityEngine`

- This script just needs one void function that is checking whether the game object colliding with our column, is our player bird or not by using `GetComponent` if the component is indeed our bird then we trigger our score counter, from our GameController

### ColumnPools.cs
**Dependencies** - `System.Collections, System.Collections.Generic, UnityEngine`

- This script is going to control our columns spawn rate, how many columns will be spawned at any given time, the position of the spawn, and create a pool off screen to improve performance
- We will need *ints* for our pool size, and current column
- *floats* for our spawn rate, column min, column max, time since last spawned, and spawn x position
- Lastly we need to reference our `GameObject` columnPrefab, columns, and our `Vector2` pool position
- This script will contain two void functions `Start` and `Update`
- Start will initialize our game object column pool as an array. Then we need to loop over our aray and `Instantiate` each column passing our prefab, positions, and a `Quaternion.identity` as arguments
- Update will increment our time since last spawned by `Time.deltaTime` every time we update. It will also check if our GameController.gameOver value is false, and if our time since last spawned is greater than our spawn rate. If true, then we will reset our time since last spawned to 0, we will create a random spawn y position variable with `Random.Range`, we will take the current item in our columns array and `transform.position` with our random y postion, and defined x position. We then increment our current column, and if your current column is greater or equal to our pool size then we reset our curren column to 0

### GameController.cs
**Dependencies** - `System.Collections, System.Collections.Generic, UnityEngine, UnityEngine.SceneManagement, UnityEngine.UI`

- This script is going to act as our index, and is going to control everything not directly involved with gameplay. Things like displaying our UI, determing whether or not the game is over, controlling the state of the game, etc
- We need to initialize a *bool* for our game over variable
- We need a `GameObject` reference to your gameOverText UI element
- We need to initialze a `float` for our scrollSpeed, and an `int` for our score
- Lastly we need a `Text`, and `static GameController` reference to our scoreText UI element, and game controller instance respectively
- There are going to be 2 void functions `Awake` and `Update`, and 2 public void functions that will control when our player scores, and when our player dies
- Awake is going to check if our instance is *null* or non-existent, if so then we will decalre instance equal to *this*, otherwise (else) we want to `Destroy` our gameObject, and restart our game
- Update will check to see if the game is over, and if our `Input.GetMouseButtonDown` has been clicked if so, then we use `SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex)` to reload our current scene
- Our scoring function will first check to see if the game is not over, if false then we *return*. Else increment our score variable, and update our scoreText UI element to display our score
- Our death function with simply display our gameOverText UI element whenever called, and set our gameOver variable to true

### RepeatingBackground.cs
**Dependencies** - `System.Collections, System.Collections.Generic, UnityEngine`

- This script is going to control our parallax effect and the movment of the game, and also the physics of the ground object
- We will need just two variables initialized. A reference to our `BoxCollider2D` element of our ground, and a *float* for our horizontal ground length
- We need two void funcitons `Start` and `Update` and a private void function that is responsible for transforming our background parallax
- Start is going to get reference to our ground collider component with `GetComponent`, and get the reference to our ground horizontal length with `groundCollider.size.x`
- RepositionBackground is going to use a `Vector2` that contains twice that of our ground lenght, and 0 as a y value. Then with that `Vector2` we will use `transform.position` to essentially leap frog our background as the player moves to create a parallax effect
- Update is going to be responsible for positioning our background. We will use `transform.position.x` to check and see if our current position is less than our ground length, if so then we call our previously defined function RepositionBackground

### ScrollingObject.cs
**Dependencies** - `System.Collections, System.Collections.Generic, UnityEngine`

- This script will be responsible for scrolling our entire game object. In combination with RepeatingBackground.cs script this will complete our parallax effect
- We just need one `Rigidbody2D` reference to our game object, and two void function `Start`, and `Update`
- Start is going to initialize our reference to our `Rigidbody2D`, once defined we will use `Rigidbody2D.velocity` to make the game scroll. We do this by passing in a `Vector2` that takes our `GameController.instance.scrollSpeed` as its first argument, and 0 in the y position as the second argument
- Update is going to check if our `GameController.instance.gameOver` is true, if so then we will use `Rigidbody2D.velocity` and set it to 0, to make the game stop scrolling upon death


## Play!

Okay so if you have succesfully implemented all these scripts without any compiling errors you should be able to press the play button on the top of the Unity editor and the game should run. Congratulations you've made a Unity app now! If you are interested in building the game and deploying it somewhere follow this [simple guide](https://unity3d.com/learn/tutorials/topics/mobile-touch/building-your-unity-game-android-device-testing) (this guide is for Android specifcically but it can applied to any platform really)
