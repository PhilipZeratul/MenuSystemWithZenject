# MenuSystemWithZenject

This is a Unity Menu Framework inspired by \
**MenuSystem** \
https://github.com/YousicianGit/UnityMenuSystem \
Check their talk at Unity Europe 2017 at \
https://www.youtube.com/watch?v=wbmjturGbAQ \
and **Zenject**, a Dependency Injection Framework for Unity\
https://github.com/modesttree/Zenject

I started this because my project is written with Zenject and I want the MenuSystem to work with Zenject as well. 

So inspired by the idea of MenuSystem, I did some modification to it and I think the result is quite pleasing.

## Introduction
As inspired by MenuSystem, all the menus are stored as canvas prefabs and once you assign them to GlobalInstaller component on the ProjectContext prefab, they will be injected by Zenject automatically on game start so you can access them anywhere in the code by just calling ```[Inject]```. See more about the convenience of Dependency Injection in their Documents.

At runtime, all the instance of menus are under ProjectContext hierarchy in DontDestroyOnLoad, so they are persistent.

## How to use:
1. You need Zenject for this to work. You can download it here: https://github.com/modesttree/Zenject and import it into your project.

2. You need to create a Zenject Project Context in Resource folder, all the menus will be a child in the hierarchy of this Project Context at runtime. 
```
Assets -> Create -> Zenject -> Project Context
```
3. Add GlobalInstaller.cs to Project Context's Installer list. \
GlobalInstaller.cs is where the global binding of Zenject happens.

4. You need a persistant scene that has a Zenject SceneContext (for Zenject to work) and a EventSystem (for listening UI events) in it.

5. Create a new script that derives from ```Menu<>``` i.e. StartMenu.cs
```C#
using Zenject;
using MenuSystemWithZenject;

public class StartMenu : Menu<StartMenu>
{
    private GameMenu.Factory _gameFactory;

    [Inject]
    private void Init(GameMenu.Factory gameFactory)
    {
        _gameFactory = gameFactory;
    }

    public void OnStartClick()
    {
        _gameFactory.Create().Open();
    }
}
```
The above code declares a StartMenu and open the GameMenu when start button is clicked. And don't forget to create the GameMenu as well, it should derive from ```Menu<GameMenu>```, and you can write your own function behaviours.

6. All the menus are individual canvases and need to be a prefab, add all the prefabs to GlobalInstaller to the Project Context prefab.

7. And you are good to go.

## Some use tips:
1. You need to use the ```Menu.Factory.Create()``` to find the instance of the menu so it get injected by Zenject when created.

2. All the menus inhereit from Menu has ```OnBackPress()``` method that will destroy/disable it self when called based on the setting of this menu.

3. ```OnMenuEnabled``` Action in MenuManager.cs is called when Menu is enabled, you can override ```OnEnable()``` method to do some notification stuff.

4. You can override ```OpenAnimation()``` and ```CloseAnimation()``` to add animation when Menu is opened or closed.

5. ```public void GoToMenu(Menu instance, bool shouldCloseAlwaysOnTopMenu = false)``` will let you jump to ```Menu instance```, closing all the menus in between.

6. ```AlwaysKeepOnTop``` is a tag that will make the Menu on top of all the Menus without it, ```OnBackPressed()``` on other Menus will not close Menus with ```AlwaysKeepOnTop``` set to true, you can close it specifically call this Menu's ```OnBackPressed()```. Good to use for overlay menus.
