# To-do
## Play client/2D sound through script
## Physics
## Manipulate position/size/rotation of 3D model
## 3D attachment points?
## Click+Drag 3D
## Raycast
## Random Number Generation
## Instantiate new 3D object
## Zone interactions
## Object permanence
## Player spawn/checkpoint manipulation
## Player alive/dead state manipulation
## Player default state manipulation
## Register custom 3D object
## Change GUI window appearance
## Change player default model
## Add brick textures
## Replace major feature
## Keep GUI element at the front
## Display 3D model in GUI
## Control camera
## Make console/chat commands (+ admin or specific player only commands)
## Control brick ownership
## Control effects of trust (full/build) and whether certain bricks require them
## Draw a line
## Create an effect (emitter) which makes use of static models
## Ascertain possible modification of rendering/shading
## Animate GUI elements
## Modify transparency of GUI elements live (fade-in effect)
## Change mouse cursor
## Create non-flat terrain
## Simple cloth simulation applied to a model (start with plane?)
## Client/Server interaction
## I/O Operation
## Live altering of model textures (for painting)

# General

## Basic network architecture?

    %count = ClientGroup.getCount();
    for(%i = 0; %i < %count; %i++)
    {
        %client = ClientGroup.getObject(%i);
        commandToClient(%client, 'command'[, args]);
    }

- commandToClient calls clientCmd[command] on a remote client
- Can only send up to 256 characters
- There is also commandToServer which calls serverCmd[command] on the server

## Get distance between two points

    %distance = vectorDist(%firstPos, %secondPos);

## Overwrite/Add to existing functions:

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

### List of (currently) known functions:
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
    - %b = Fire, Jump, Crouch, Jet
- WeaponImage::onMount(%t, %p, %s)
    - %s == 0: UseTool
- WeaponImage::onUnMount(%t, %p, %s)
    - %s == 0: UnUseTool

## Tick/Update functionality:

    $tickTime = 0;

    schedule($tickTime, 0, "Update");

    function Update()
    {
        // code
        schedule($tickTime, 0, "Update");
    }

# GUI
## Mouse controls in GUI:

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

    function GuiMouseEventCtrlName::onMouseEnter(%this, %a, %b, %c) 
    {
        echo("onMouseEnter");
    }

    function GuiMouseEventCtrlName::onMouseLeave(%this, %a, %b, %c) 
    {
        echo("onMouseLeave");
    }

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

    function InventoryGuiMouse::onRightMouseDown(%this, %a, %pos) 
    {
        echo("InventoryGuiMouse: onRightMouseDown");
    }

    function InventoryGuiMouse::onRightMouseUp(%this, %a, %pos) 
    {
        echo("InventoryGuiMouse: onRightMouseUp");
    }

    function InventoryGuiMouse::onRightMouseDragged(%this, %a, %pos, %c) 
    {
        echo("InventoryGuiMouse: onRightMouseDragged");
    }

- This works for any GUIControl

## Cursor position in GUI:

    %cursorPos = Canvas.getCursorPos();

    %cursorXPos = getWord(%cursorPos, 0);
    %cursorYPos = getWord(%cursorPos, 1);

## onWake:

    function TargetGUIControl::onWake()
    {
        // code
    }

## Binding for Toggling GUI:

    $remapDivision[$remapCount] = "Category";
    $remapName[$remapCount] = "Action";
    $remapCmd[$remapCount] = "ToggleGUIControl";
    $remapCount++;

    function ToggleGUIControl(%toggle)
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