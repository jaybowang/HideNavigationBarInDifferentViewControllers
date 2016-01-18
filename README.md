# HideNavigationBarInDifferentViewControllers
Use UINavigationControllerDelegate to hide navigation bar in different view controllers.

Assume that you have many kinds of view controllers. Some of them need navigation bar and others don't. 
A common solution is to call `setNavigationBarHidden` in each view controller. Like below:

    override func viewWillAppear(animated: Bool) {
        super.viewWillAppear(animated)
        navigationController?.setNavigationBarHidden(true/false, animated: animated)
    }

And hide navigation bar when view controller disappears:

    override func viewWillDisappear(animated: Bool) {
        super.viewWillDisappear(animated)
        
        if isMovingFromParentViewController() // or other logic
        {
            navigationController?.setNavigationBarHidden(wasNavigationBarHidden, animated: animated)
        }
    }
    
    
But there're a few problems using this solution when it comes to a situation like this:

**`HideNavigationBarViewController1` is the root view controller in a navigation stack. 
`HideNavigationBarViewController1` can push to `HideNavigationBarViewController2` and `ShowNavigationBarViewController1`. 
`HideNavigationBarViewController2` and `ShowNavigationBarViewController1` can push to other view controllers just like `HideNavigationBarViewController1` do.**


It's very complicated to call `setNavigationBarHidden` every now and then.
When two view controllers of hide-navigation-bar-view-controller come along, the navigation bar will flash when push and pop.

-
`UINavigationControllerDelegate` is very helpful to do a such thing! EASY.
```
extension Xxx: UINavigationControllerDelegate {
    
    func navigationController(navigationController: UINavigationController, willShowViewController viewController: UIViewController, animated: Bool) {

        switch viewController {
        
        case viewController as HideNavigationBarViewController1, viewController as HideNavigationBarViewController2, viewController as HideNavigationBarViewController3:
            navigationController.setNavigationBarHidden(true, animated: animated)
            
        default:
            navigationController.setNavigationBarHidden(false, animated: animated)
        }
    }

}
```

And one more step to enable the interactive pop gesture that is disabled by `setNavigationBarHidden`.

    override func viewDidAppear(animated: Bool) {
        super.viewDidAppear(animated)
        
        // http://stackoverflow.com/a/28919337/1573750
        // Do this in the root view controller of a navigation controller
        navigationController?.interactivePopGestureRecognizer?.delegate = self as? UIGestureRecognizerDelegate
    }
