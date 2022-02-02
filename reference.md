# Blockland Reference Doc
## Contents
### [0. To-do](#0-to-do)
### 1. Data Strucutres
#### [1.1 List](#11-list)
#### [1.2 Stack](#12-stack)
#### [1.3 Queue](#13-queue)
### 2. General
#### [2.1 Overwrite/Add-to Existing Functions](#21-overwriteadd-to-existing-functions)
##### [2.1.1 List of (currently) Known Functions](#211-list-of-currently-known-functions)
#### [2.2 Tick/Update Function](#22-tickupdate-function)
### 3. GUI
#### [3.1 Display 3D Model](#31-display-3d-model)
#### [3.2 Mouse Controls](#32-mouse-controls)
#### [3.3 Get Cursor Position](#33-get-cursor-position)
#### [3.4 onWake](#34-onwake)
#### [3.5 Binding for Toggling GUI](#35-binding-for-toggling-gui)
### 4. Network Architecture
#### [4.1 Basic Client/Server Commands](#41-basic-clientserver-commands)
### 5. Math
#### [5.1 Distance Between Two Points](#51-distance-between-two-points)


# 0. To-do
- Play client/2D sound through script
    - 2D music block add-on
- Physics
    - Physics brick add-on
- Manipulate position/size/rotation of 3D model
    - Hatmod?
- 3D attachment points?
    - ... Hatmod?
- Click+Drag 3D
- Raycast
    - Laser pointer add-on
    - https://forum.blockland.us/index.php?topic=224122.0
- Random Number Generation
    - Random number events add-on
- Instantiate new 3D object
    -  Hatmod
- Zone interactions
    - Zone events add-on(s) (there are two of them?)
- Object permanence
- Player spawn/checkpoint manipulation
    - Global spawn add-on
- Player alive/dead state manipulation
- Player default state manipulation
- Register custom 3D object
    - Hatmod
- Change GUI window appearance
- Change player default model
- Add brick textures
- Replace major features
- Keep GUI elements at front
- Control camera
    - Camera events add-on
- Make console/chat commands (+ admin or specific player only commands)
    - Lots of add-ons... but also Hatmod
- Control brick ownership
- Control effects of trust (full/build) and whether certain bricks require them
- Draw a line, 2D/3D, serverside/client-only
    - Grappling hook does this, in 3D at least
- Create an effect (emitter) which makes use of static models
- Possible modifications of rendering/shading?
- Animate GUI elements
    - BlocklandGlass has animated snowflakes effect
- Modify transparency of GUI elements (fade-in/out)
- Change mouse cursor
- Create non-flat terrain
- Vertex/bone manipulation of models (cloth physics, etc.)
- Detailed Client/Server interaction
- I/O Operation
- Live altering of model textures (for painting)

# 1. Data Structures
I'm not sure whether these exist in TGE. For now, I made my own, as they're fairly simple to set up.

## 1.1 List

    function CreateList(%name)
    {
        %newList = new ScriptObject(%name)
        {
            class = List;
            count = 0;
        };

        return %newList;
    }

    // Adds an element at the end of a List.
    function List::Add(%this, %value)
    {
        %this.list[%this.count] = %value;
        %this.count += 1;
    }

    // Checks whether the specified element exists or not in a List.
    function List::Contains(%this, %value)
    {
        for(%i = 0; %i < %this.count; %i++)
        {
            if(%this.list[%i] $= %value) return true;
        }

        return false;
    }

    // Removes the (last?) occurrence of the specified element.
    function List::Remove(%this, %value)
    {
        %removeAtIndex = "";

        for(%i = 0; %i < %this.count; %i++)
        {
            if(%this.list[%i] $= %value)
            {
                %removeAtIndex = %i;
            }
        }

        if(%removeAtIndex $= "")
        {
            echo(%value SPC "does not exist in" SPC %this);
            return;
        }
        else
        {
            %this.count -= 1;
        }

        if(%removeAtIndex == %this.count)
        {
            %this.list[%removeAtIndex] = "";
            return;
        }

        for(%i = %removeAtIndex; %i < %this.count; %i++)
        {
            %this.list[%i] = %this.list[%i + 1];
        }

        %this.list[%this.count] = "";
    }

    // Removes all items from the List.
    function List::Clear(%this)
    {
        for(%i = 0; %i < %this.count; %i++)
        {
            %this.list[%i] = "";
        }

        %this.count = 0;
    }

- List isn't feature complete

## 1.2 Stack

    function CreateStack(%name)
    {
        %newStack = new ScriptObject(%name)
        {
            class = Stack;
            count = 0;
        };

        return %newStack;
    }

    // Inserts an item at the top of the stack.
    function Stack::Push(%this, %value)
    {
        %this.stack[%this.count] = %value;
        %this.count += 1;
    }

    // Returns the top item from the stack.
    function Stack::Peek(%this)
    {
        return %this.stack[%this.count - 1];
    }

    // Removes and returns the top item from the stack.
    function Stack::Pop(%this)
    {
        %valueToReturn = %this.stack[%this.count - 1];

        if(!isObject(%valueToReturn))
        {
            echo("Invalid Operation Exception");
            return;
        }

        %this.stack[%this.count - 1] = "";

        %this.count -= 1;

        return %valueToReturn;
    }

    // Stacks traditionally don't have a Remove so this is kind've cheating but I did it anyways
    // Removes the (last?) occurrence of the specified element.
    function Stack::Remove(%this, %value)
    {
        %removeAtIndex = "";

        for(%i = 0; %i < %this.count; %i++)
        {
            if(%this.stack[%i] $= %value)
            {
                %removeAtIndex = %i;
            }
        }

        if(%removeAtIndex $= "")
        {
            echo(%value SPC "does not exist in" SPC %this);
            return;
        }
        else
        {
            %this.count -= 1;
        }

        if(%removeAtIndex == %this.count)
        {
            %this.stack[%removeAtIndex] = "";
            return;
        }

        for(%i = %removeAtIndex; %i < %this.count; %i++)
        {
            %this.stack[%i] = %this.stack[%i + 1];
        }

        %this.stack[%this.count] = "";
    }

    // Checks whether an item exists in the stack or not.
    function Stack::Contains(%this, %value)
    {
        for(%i = 0; %i < %this.count; %i++)
        {
            if(%this.stack[%i] $= %value) return true;
        }

        return false;
    }

    // Removes all items from the stack.
    function Stack::Clear(%this)
    {
        for(%i = 0; %i < %this.count; %i++)
        {
            %this.stack[%i] = "";
        }

        %this.count = 0;
    }

## 1.3 Queue

    function CreateQueue(%name)
    {
        %newQueue = new ScriptObject(%name)
        {
            class = Queue;
            count = 0;
        };

        return %newQueue;
    }

    // Adds an item into the queue.
    function Queue::Enqueue(%this, %value)
    {
        %this.queue[%this.count] = %value;
        %this.count += 1;
    }

    // Returns an item from the beginning of the queue and removes it from the queue.
    function Queue::Dequeue(%this)
    {
        %valueToReturn = %this.queue[0];

        if(!isObject(%valueToReturn))
        {
            echo("Invalid Operation Exception");
            return;
        }

        for(%i = 0; %i < %this.count - 1; %i++)
        {
            %this.queue[%i] = %this.queue[%i + 1];
        }

        %this.queue[%this.count - 1] = "";

        %this.count -= 1;

        return %valueToReturn;
    }

    // Returns the first item from the queue without removing it.
    function Queue::Peek(%this)
    {
        return %this.queue[0];
    }

    // Checks whether an item exists in the queue or not.
    function Queue::Contains(%this, %value)
    {
        for(%i = 0; %i < %this.count; %i++)
        {
            if(%this.queue[%i] $= %value) return true;
        }

        return false;
    }

    // Removes all items from the queue.
    function Queue::Clear(%this)
    {
        for(%i = 0; %i < %this.count; %i++)
        {
            %this.queue[%i] = "";
        }

        %this.count = 0;
    }

# 2. General
## 2.1 Overwrite/Add-to Existing Functions

    package PackageName
    {
        function serverCmdLight(%c)
        {
            // code...
            Parent::serverCmdLight(%c);
        }
    }
    ActivatePackage(PackageName);

- Parent::serverCmdLight(%c); plays the normal functionality

### 2.1.1 List of (currently) Known Functions
- serverCmdCancelBrick(%c)
    - NumPad 0
- serverCmdShiftBrick(%c, %x, %y, %z)
    - %z == -1: BrickPlateDown
        - NumPad 1
    - %x == -1: BrickToward
        - Numpad 2
    - %z == 1: BrickPlateUp
        - NumPad 3
    - %y == 1: BrickLeft
        - NumPad 4
    - %z == -3: BrickDown
        - NumPad 5
    - %y == -1: BrickRight
        - NumPad 6
    - %x == 1: BrickAway
        - NumPad 8
    - %z == 3: BrickUp
        - NumPad +
- serverCmdRotateBrick(%c, %dir)
    - %dir == 1: BrickRotateCW
        - NumPad 7
    - %dir != 1: BrickRotateCCW
        - NumPad 9
- serverCmdPlantBrick(%c)
    - NumPad Enter
- serverCmdLight(%c)
    - R
- serverCmdPrevSeat(%c)
    - ?
- serverCmdNextSeat(%c)
    - ?
- Armor::onTrigger(%t, %p, %b, %v)
    - %v == 0: onUp
    - %v != 0: onDown
    - %b = Fire, Jump, Crouch, Jet (not sure in which order)
- WeaponImage::onMount(%t, %p, %s)
    - %s == 0: UseTool
- WeaponImage::onUnMount(%t, %p, %s)
    - %s == 0: UnUseTool

## 2.2 Tick/Update Function

    $tickTime = 0;

    schedule($tickTime, 0, "Update");

    function Update()
    {
        // code
        schedule($tickTime, 0, "Update");
    }

# 3. GUI
## 3.1 Display 3D Model

    function ObjectViewTest()
    {
        %newGuiObjectView = new GuiObjectView(ObjectViewer) {
            profile = "GuiDefaultProfile";
            horizSizing = "right";
            vertSizing = "bottom";
            position = "258 123";
            extent = "64 64";
            minExtent = "8 2";
            enabled = "1";
            visible = "1";
            clipToParent = "1";
            cameraZRot = "0";
            forceFOV = "0";
            lightDirection = "-0.57735 -0.57735 -0.57735";
            lightColor = "0.600000 0.580000 0.500000 1.000000";
            ambientColor = "0.300000 0.300000 0.300000 1.000000";
        };

        Canvas.add(%newGuiObjectView);

        %newGuiObjectView.setObject("", "Add-Ons/Weapon_Sword/sword.dts", "", 100);
    }

- I don't know what the parameters of GuiObjectView::setObject are for other than the model path
    - setObject has been replaced with setModel in Torque3D, which only uses the model path as a parameter,
    so finding documentation on the old setObject has proven a little bit difficult
    - setObject won't work if the other parameters are left out

## 3.2 Mouse Controls

https://torque-3d.readthedocs.io/en/latest/script/class/GuiMouseEventCtrl.html

    %GUIControl = new GuiBitmapCtrl(GuiBitmapCtrlName)
    {
        // properties
    };

    %GUIControl.mouseCtrl = new GuiMouseEventCtrl(GuiMouseEventCtrlName)
    {
        image = %GUIControl;
        extent = %GUIControl.extent;
        position = "0 0";
    };

    %GUIControl.add(%GUIControl.mouseCtrl);

    Canvas.add(%GUIControl);


    // Cursor Enter/Leave/Move

    function GuiMouseEventCtrlName::onMouseEnter(%this, %a, %b, %c) 
    {
        echo("onMouseEnter");
    }

    function GuiMouseEventCtrlName::onMouseLeave(%this, %a, %b, %c) 
    {
        echo("onMouseLeave");
    }

    function GuiMouseEventCtrlName::onMouseMove(%this)
    {
        echo("onMouseMove");
    }


    // Left mouse

    function GuiMouseEventCtrlName::onMouseDown(%this, %a, %pos) 
    {
        echo("onMouseDown");
    }

    function GuiMouseEventCtrlName::onMouseUp(%this, %a, %pos) 
    {
        echo("onMouseUp");
    }

    function GuiMouseEventCtrlName::onMouseDragged(%this, %a, %pos, %c) 
    {
        echo("onMouseDragged");
    }


    // Right mouse

    function GuiMouseEventCtrlName::onRightMouseDown(%this, %a, %pos) 
    {
        echo("onRightMouseDown");
    }

    function GuiMouseEventCtrlName::onRightMouseUp(%this, %a, %pos) 
    {
        echo("onRightMouseUp");
    }

    function GuiMouseEventCtrlName::onRightMouseDragged(%this, %a, %pos, %c) 
    {
        echo("onRightMouseDragged");
    }

- This works for any GUIControl
- MiddleMouse controls don't work with GuiMouseEventCtrl
    - It should be possible to get around this by directly taking an input and then sending through the cursor location
    - I have not yet found a way to properly intercept inputs while a GUI is open, however

## 3.3 Get Cursor Position

    %cursorPos = Canvas.getCursorPos(); // returns a vector formatted as a string, ex: "210 30"

    %cursorXPos = getWord(%cursorPos, 0);
    %cursorYPos = getWord(%cursorPos, 1);

## 3.4 onWake:

    function TargetGUIControl::onWake()
    {
        // code
    }

## 3.5 Binding for Toggling GUI

    $remapDivision[$remapCount] = "BindingDivisionName";
    $remapName[$remapCount] = "BindingName";
    $remapCmd[$remapCount] = "ToggleFunctionName";
    $remapCount++;

    function ToggleFunctionName(%toggle)
    {
        if(%toggle)
        {
            if(Testgui.isAwake())
            {
                canvas.popDialog(Testgui);
            }
            else
            {
                canvas.pushDialog(Testgui);
                Testgui.extent = "150 150";
            }
        }
    }

# 4. Network Architecture
## 4.1 Basic Client/Server Commands

    %count = ClientGroup.getCount();
    for(%i = 0; %i < %count; %i++)
    {
        %client = ClientGroup.getObject(%i);
        commandToClient(%client, 'command'[, args]);
    }

- commandToClient calls clientCmd[command] on a remote client
- Can only send up to 256 characters
- There is also commandToServer which calls serverCmd[command] on the server

# 5. Math
## 5.1 Distance Between Two Points

    %distance = vectorDist(%firstPos, %secondPos);

