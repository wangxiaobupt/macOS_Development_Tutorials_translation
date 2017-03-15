# OS X NSTask教程

#### [原文地址](https://www.raywenderlich.com/125071/nstask-tutorial-os-x) 翻译：[DeveloperLx](http://weibo.com/DeveloperLx)


<div class="content-wrapper">
    <div id="attachment_129761" style="width: 260px" class="wp-caption alignright">
        <a href="https://koenig-media.raywenderlich.com/uploads/2016/03/NSTask-for-mac-feature.png"
        rel="attachment wp-att-129761" sl-processed="1">
            <img src="https://koenig-media.raywenderlich.com/uploads/2016/03/NSTask-for-mac-feature-250x250.png"
            alt="See a practical example of using NSTask!" width="250" height="250"
            class="size-thumbnail wp-image-129761 bordered" srcset="https://koenig-media.raywenderlich.com/uploads/2016/03/NSTask-for-mac-feature-250x250.png 250w, https://koenig-media.raywenderlich.com/uploads/2016/03/NSTask-for-mac-feature-320x320.png 320w, https://koenig-media.raywenderlich.com/uploads/2016/03/NSTask-for-mac-feature.png 500w, https://koenig-media.raywenderlich.com/uploads/2016/03/NSTask-for-mac-feature-32x32.png 32w, https://koenig-media.raywenderlich.com/uploads/2016/03/NSTask-for-mac-feature-64x64.png 64w, https://koenig-media.raywenderlich.com/uploads/2016/03/NSTask-for-mac-feature-96x96.png 96w, https://koenig-media.raywenderlich.com/uploads/2016/03/NSTask-for-mac-feature-128x128.png 128w"
            sizes="(max-width: 250px) 100vw, 250px">
        </a>
        <p class="wp-caption-text">
            See a practical example of using NSTask!
        </p>
    </div>
    <div class="note">
        <em>
            Update note:
        </em>
        This NSTask Tutorial for OS X has been updated to Swift by
        <a href="https://www.raywenderlich.com/u/warrenburton" sl-processed="1">
            Warren Burton
        </a>
        . The original tutorial was written by
        <a href="https://www.raywenderlich.com/u/macandyp" sl-processed="1">
            Andy Pereira
        </a>
        .
    </div>
    <p>
        Your Mac has a fully-fledged version of UNIX as its operating system,
        which means it has a massive amount of pre-installed command line utilities
        and scripting languages. Swift, Perl, Python, Bash, Ruby, plus anything
        you can install. Wouldn’t it be nice to use to that power to create awesome
        apps?
    </p>
    <p>
        <code>
            NSTask
        </code>
        allows you to execute another program on your machine as a subprocess
        and monitor its execution state while your main program continues to run.
        For example, you could run the
        <code>
            ls
        </code>
        command to display a directory listing — from right inside your app!
    </p>
    <p>
        A good analogy for
        <code>
            NSTask
        </code>
        is a parent-child relationship. A parent can create a child and tell it
        to do certain things, and (theoretically) the child must obey.
        <code>
            NSTask
        </code>
        behaves in a similar fashion; you start a “child” program, give it instructions
        and tell it where to report any output or errors. Better yet — it’s way
        more obedient than your average toddler :]
    </p>
    <p>
        A great use for
        <code>
            NSTask
        </code>
        is to provide a front-end GUI to command line programs. Command line programs
        are powerful, but they require you to remember exactly where they live
        on your machine, how to call them and what options or arguments you can
        provide to the program. Adding a GUI on the front end can provide the user
        with a great deal of control — without having to be a command line guru!
    </p>
    <p>
        This tutorial includes an
        <code>
            NSTask
        </code>
        example that shows you how to execute a simple command program with arguments
        and display its standard output as it runs in a text field. By the end,
        you’ll be ready to use NSTasks in your own apps!
    </p>
    <div class="note">
        <p>
            <em>
                Note:
            </em>
            This tutorial assumes you have some basic familiarity with Mac OS X development
            and the Terminal. If you are completely new to programming for the Mac,
            check out our
            <a href="/?p=17811" sl-processed="1">
                beginning Mac OS X development tutorial series
            </a>
            .
        </p>
    </div>
    <h2>
        Getting Started
    </h2>
    <p>
        To keep the focus squarely on NSTask, I’ve created a starter project for
        you that contains a basic user interface.
        <a href="https://koenig-media.raywenderlich.com/uploads/2016/04/TasksProject-starter_s3.zip"
        sl-processed="1">
            Download the project
        </a>
        , open it in Xcode, and build and run.
    </p>
    <p>
        The starter app has one window, as shown:
    </p>
    <p>
        <img src="https://koenig-media.raywenderlich.com/uploads/2016/02/initial_view-562x500.png"
        alt="initial_view" width="562" height="500" class="aligncenter size-large wp-image-126818"
        srcset="https://koenig-media.raywenderlich.com/uploads/2016/02/initial_view-562x500.png 562w, https://koenig-media.raywenderlich.com/uploads/2016/02/initial_view-360x320.png 360w, https://koenig-media.raywenderlich.com/uploads/2016/02/initial_view-768x683.png 768w, https://koenig-media.raywenderlich.com/uploads/2016/02/initial_view.png 956w"
        sizes="(max-width: 562px) 100vw, 562px">
    </p>
    <p>
        This window has the title “TasksProject”. It has a simple GUI that will,
        by invoking a shell script, let you build an iOS project, create an
        <code>
            ipa
        </code>
        and observe what is happening.
    </p>
    <h2>
        Creating Your First NSTask
    </h2>
    <p>
        The
        <code>
            NSTask
        </code>
        example will be to build and package an iOS app into an
        <code>
            ipa
        </code>
        file by using
        <code>
            NSTask
        </code>
        to run some command line tools in the background. Most of the basic UI
        functionality is in place — your job is do the heavy lifting with
        <code>
            NSTask
        </code>
        .
    </p>
    <div class="note">
        <p>
            <em>
                Note:
            </em>
            It’s recommended that you have an iOS Developer account with Apple, as
            you’ll need the proper certificates and provisioning profile to create
            an
            <code>
                ipa
            </code>
            file that can be installed on one of your devices. Don’t worry if you
            don’t, though, as you’ll be able to follow the entire tutorial even without
            an account.
        </p>
    </div>
    <p>
        You are now going to work on the embedded View Controller titled “Tasks
        View Controller”. The first section in the window asks the user to select
        an Xcode project directory. To save time, rather than having to select
        a directory manually every time you run this app while testing, you’ll
        hard-code it to one of your own Xcode project directories.
    </p>
    <p>
        To do this, head back to Xcode and open
        <em>
            TasksViewController.swift
        </em>
        . Take a look at the properties and methods under the comment “Window
        Outlets”:
    </p>
    <div class="wp_codebox">
        <table>
            <tbody>
                <tr id="p1250711">
                    <td class="code" id="p125071code1">
                        <pre class="swift" style="font-family:monospace;">
                            <span style="color: #11740a; font-style: italic;">
                                //View Controller Outlets
                            </span>
                            @IBOutlet
                            <span style="color: #a61390;">
                                var
                            </span>
                            outputText
                            <span style="color: #002200;">
                                :
                            </span>
                            <span style="color: #400080;">
                                NSTextView
                            </span>
                            <span style="color: #002200;">
                                !
                            </span>
                            @IBOutlet
                            <span style="color: #a61390;">
                                var
                            </span>
                            spinner
                            <span style="color: #002200;">
                                :
                            </span>
                            <span style="color: #400080;">
                                NSProgressIndicator
                            </span>
                            <span style="color: #002200;">
                                !
                            </span>
                            @IBOutlet
                            <span style="color: #a61390;">
                                var
                            </span>
                            projectPath
                            <span style="color: #002200;">
                                :
                            </span>
                            <span style="color: #400080;">
                                NSPathControl
                            </span>
                            <span style="color: #002200;">
                                !
                            </span>
                            @IBOutlet
                            <span style="color: #a61390;">
                                var
                            </span>
                            repoPath
                            <span style="color: #002200;">
                                :
                            </span>
                            <span style="color: #400080;">
                                NSPathControl
                            </span>
                            <span style="color: #002200;">
                                !
                            </span>
                            @IBOutlet
                            <span style="color: #a61390;">
                                var
                            </span>
                            buildButton
                            <span style="color: #002200;">
                                :
                            </span>
                            <span style="color: #400080;">
                                NSButton
                            </span>
                            <span style="color: #002200;">
                                !
                            </span>
                            @IBOutlet
                            <span style="color: #a61390;">
                                var
                            </span>
                            targetName
                            <span style="color: #002200;">
                                :
                            </span>
                            <span style="color: #400080;">
                                NSTextField
                            </span>
                            <span style="color: #002200;">
                                !
                            </span>
                        </pre>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
    <p>
        All of these properties correspond to the
        <em>
            Tasks View Controller Scene
        </em>
        in
        <em>
            Main.storyboard
        </em>
        . Notice the
        <code>
            projectPath
        </code>
        property — this is the one you want to change.
    </p>
    <p>
        Open
        <em>
            Main.storyboard
        </em>
        and click on the
        <em>
            Project Location
        </em>
        item. You’ll find it 4 levels deep in the object hierarchy; it’s a child
        of the
        <em>
            Stack View
        </em>
        . In the
        <code>
            Attributes Inspector
        </code>
        , under
        <em>
            Path Control
        </em>
        , find the
        <em>
            Path
        </em>
        element:
    </p>
    <p>
        <img src="https://koenig-media.raywenderlich.com/uploads/2016/03/set_path_1b-700x385.png"
        alt="set_path_1b" width="700" height="385" class="aligncenter size-large wp-image-128235"
        srcset="https://koenig-media.raywenderlich.com/uploads/2016/03/set_path_1b-700x385.png 700w, https://koenig-media.raywenderlich.com/uploads/2016/03/set_path_1b-480x264.png 480w, https://koenig-media.raywenderlich.com/uploads/2016/03/set_path_1b-768x423.png 768w"
        sizes="(max-width: 700px) 100vw, 700px">
    </p>
    <p>
        Set
        <em>
            Path
        </em>
        to a directory on your machine that contains an iOS project. Make sure
        you use the
        <i>
            parent
        </i>
        directory of a project, not the
        <code>
            .xcodeproj
        </code>
        file itself.
    </p>
    <div class="note">
        <p>
            <em>
                Note:
            </em>
            If you don’t have any iOS projects on your machine,
            <a href="https://koenig-media.raywenderlich.com/uploads/2016/04/SuperDuperApp_s3.zip"
            sl-processed="1">
                download a simple project here
            </a>
            and unzip it to a location on your machine. Then set the
            <code>
                Path
            </code>
            property in your application using the instructions above. For example,
            if you unzip the package on your desktop, you would set
            <code>
                Path
            </code>
            to
            <code>
                <br>
                /Users/YOUR_USERNAME_HERE/Desktop/SuperDuperApp
            </code>
            .
        </p>
    </div>
    <p>
        Now that you have a default source project path in your app to facilitate
        testing, you will also need a default destination path. Open
        <em>
            Main.storyboard
        </em>
        and click on the
        <em>
            Build Repository
        </em>
        item.
    </p>
    <p>
        In the Attributes Inspector, find
        <em>
            Path
        </em>
        item under
        <em>
            Path Control
        </em>
        :
    </p>
    <p>
        <img src="https://koenig-media.raywenderlich.com/uploads/2016/03/set_path_2b-700x385.png"
        alt="set_path_2b" width="700" height="385" class="aligncenter size-large wp-image-128236"
        srcset="https://koenig-media.raywenderlich.com/uploads/2016/03/set_path_2b-700x385.png 700w, https://koenig-media.raywenderlich.com/uploads/2016/03/set_path_2b-480x264.png 480w, https://koenig-media.raywenderlich.com/uploads/2016/03/set_path_2b-768x423.png 768w"
        sizes="(max-width: 700px) 100vw, 700px">
    </p>
    <p>
        Set the
        <em>
            Path
        </em>
        entry to a directory on your machine that’s easy to find, like the Desktop.
        This is where the
        <code>
            .ipa
        </code>
        file created by this application will be placed.
    </p>
    <p>
        There are two additional fields in the
        <code>
            Tasks View Controller Scene
        </code>
        you need to know about: the
        <em>
            Target Name
        </em>
        and an associated text field.
    </p>
    <p>
        <img src="https://koenig-media.raywenderlich.com/uploads/2016/02/initial_view_2-562x500.png"
        alt="initial_view_2" width="562" height="500" class="aligncenter size-large wp-image-126819"
        srcset="https://koenig-media.raywenderlich.com/uploads/2016/02/initial_view_2-562x500.png 562w, https://koenig-media.raywenderlich.com/uploads/2016/02/initial_view_2-360x320.png 360w, https://koenig-media.raywenderlich.com/uploads/2016/02/initial_view_2-768x683.png 768w, https://koenig-media.raywenderlich.com/uploads/2016/02/initial_view_2.png 956w"
        sizes="(max-width: 562px) 100vw, 562px">
    </p>
    <ol>
        <li>
            <em>
                Target Name
            </em>
            is designated for the name of the iOS
            <code>
                Target
            </code>
            you want to build.
        </li>
        <li>
            The text area below Target Name will display output from the
            <code>
                NSTask
            </code>
            object in your project as it runs.
        </li>
    </ol>
    <p>
        Don’t know the target name of your iOS project? To find it, select your
        project in Xcode’s project navigator and look under
        <em>
            TARGETS
        </em>
        in the
        <em>
            Info
        </em>
        tab. The screenshot below shows where to find this for a sample project
        called “SuperDuperApp”:
    </p>
    <p>
        <img src="https://koenig-media.raywenderlich.com/uploads/2016/02/target_name-700x429.png"
        alt="target_name" width="700" height="429" class="aligncenter size-large wp-image-126825"
        srcset="https://koenig-media.raywenderlich.com/uploads/2016/02/target_name-700x429.png 700w, https://koenig-media.raywenderlich.com/uploads/2016/02/target_name-480x294.png 480w, https://koenig-media.raywenderlich.com/uploads/2016/02/target_name-768x471.png 768w, https://koenig-media.raywenderlich.com/uploads/2016/02/target_name.png 1282w"
        sizes="(max-width: 700px) 100vw, 700px">
    </p>
    <p>
        Remember the target name — you’ll be entering it into the running app
        later on.
    </p>
    <p>
        Let’s start fleshing out the bits of code that will run when the “Build”
        button is pressed.
    </p>
    <h2>
        Preparing the Spinner
    </h2>
    <p>
        Open
        <em>
            TasksViewController.swift
        </em>
        and add the following code to
        <code>
            startTask(_:)
        </code>
        :
    </p>
    <div class="wp_codebox">
        <table>
            <tbody>
                <tr id="p1250712">
                    <td class="code" id="p125071code2">
                        <pre class="swift" style="font-family:monospace;">
                            <span style="color: #11740a; font-style: italic;">
                                //1.
                            </span>
                            outputText.string
                            <span style="color: #002200;">
                                =
                            </span>
                            <span style="color: #bf1d1a;">
                                ""
                            </span>
                            &nbsp;
                            <span style="color: #a61390;">
                                if
                            </span>
                            <span style="color: #a61390;">
                                let
                            </span>
                            projectURL
                            <span style="color: #002200;">
                                =
                            </span>
                            projectPath.url,
                            <span style="color: #a61390;">
                                let
                            </span>
                            repositoryURL
                            <span style="color: #002200;">
                                =
                            </span>
                            repoPath.url
                            <span style="color: #002200;">
                                {
                            </span>
                            &nbsp;
                            <span style="color: #11740a; font-style: italic;">
                                //2.
                            </span>
                            <span style="color: #a61390;">
                                let
                            </span>
                            projectLocation
                            <span style="color: #002200;">
                                =
                            </span>
                            projectURL.path
                            <span style="color: #a61390;">
                                let
                            </span>
                            finalLocation
                            <span style="color: #002200;">
                                =
                            </span>
                            repositoryURL.path &nbsp;
                            <span style="color: #11740a; font-style: italic;">
                                //3.
                            </span>
                            <span style="color: #a61390;">
                                let
                            </span>
                            projectName
                            <span style="color: #002200;">
                                =
                            </span>
                            projectURL.lastPathComponent
                            <span style="color: #a61390;">
                                let
                            </span>
                            xcodeProjectFile
                            <span style="color: #002200;">
                                =
                            </span>
                            projectLocation
                            <span style="color: #002200;">
                                +
                            </span>
                            <span style="color: #bf1d1a;">
                                "/
                                <span style="color: #2400d9;">
                                    \(
                                </span>
                                projectName).xcodeproj"
                            </span>
                            &nbsp;
                            <span style="color: #11740a; font-style: italic;">
                                //4.
                            </span>
                            <span style="color: #a61390;">
                                let
                            </span>
                            buildLocation
                            <span style="color: #002200;">
                                =
                            </span>
                            projectLocation
                            <span style="color: #002200;">
                                +
                            </span>
                            <span style="color: #bf1d1a;">
                                "/build"
                            </span>
                            &nbsp;
                            <span style="color: #11740a; font-style: italic;">
                                //5.
                            </span>
                            <span style="color: #a61390;">
                                var
                            </span>
                            arguments
                            <span style="color: #002200;">
                                :
                            </span>
                            <span style="color: #002200;">
                                [
                            </span>
                            <span style="color: #a61390;">
                                String
                            </span>
                            <span style="color: #002200;">
                                ]
                            </span>
                            <span style="color: #002200;">
                                =
                            </span>
                            <span style="color: #002200;">
                                [
                            </span>
                            <span style="color: #002200;">
                                ]
                            </span>
                            arguments.append
                            <span style="color: #002200;">
                                (
                            </span>
                            xcodeProjectFile
                            <span style="color: #002200;">
                                )
                            </span>
                            arguments.append
                            <span style="color: #002200;">
                                (
                            </span>
                            targetName.stringValue
                            <span style="color: #002200;">
                                )
                            </span>
                            arguments.append
                            <span style="color: #002200;">
                                (
                            </span>
                            buildLocation
                            <span style="color: #002200;">
                                )
                            </span>
                            arguments.append
                            <span style="color: #002200;">
                                (
                            </span>
                            projectName
                            <span style="color: #002200;">
                                )
                            </span>
                            arguments.append
                            <span style="color: #002200;">
                                (
                            </span>
                            finalLocation
                            <span style="color: #002200;">
                                )
                            </span>
                            &nbsp;
                            <span style="color: #11740a; font-style: italic;">
                                //6.
                            </span>
                            buildButton.isEnabled
                            <span style="color: #002200;">
                                =
                            </span>
                            <span style="color: #a61390;">
                                false
                            </span>
                            spinner.startAnimation
                            <span style="color: #002200;">
                                (
                            </span>
                            <span style="color: #a61390;">
                                self
                            </span>
                            <span style="color: #002200;">
                                )
                            </span>
                            &nbsp;
                            <span style="color: #002200;">
                                }
                            </span>
                        </pre>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
    <p>
        Here’s a step-by-step explanation of the code above:
    </p>
    <ol>
        <li>
            <code>
                outputText
            </code>
            is the large text box in the window; it will contain all the output from
            the script that you will be running. If you run the script multiple times,
            you’ll want to clear it out between each run, so this first line sets the
            <code>
                string
            </code>
            property (contents of the text box) to an empty string.
        </li>
        <li>
            The
            <code>
                projectURL
            </code>
            and
            <code>
                repositoryURL
            </code>
            objects are
            <code>
                NSURL
            </code>
            objects, and this gets the string representations of these objects in
            order to pass them as arguments to your
            <code>
                NSTask
            </code>
            .
        </li>
        <li>
            By convention, the name of the folder and the name of the project file
            are the same. Getting the
            <code>
                lastPathComponent
            </code>
            property of the project folder contained in
            <code>
                projectURL
            </code>
            and adding an “.xcodeproj” extension gets the path to the project file.
        </li>
        <li>
            Defines the subdirectory where your task will store intermediate build
            files while it’s creating the
            <code>
                ipa
            </code>
            file as
            <code>
                build
            </code>
            .
        </li>
        <li>
            Stores the arguments in an array. This array will be passed to
            <code>
                NSTask
            </code>
            to be used when launching the command line tools to build your
            <code>
                .ipa
            </code>
            file.
        </li>
        <li>
            Disables the “Build” button and starts a spinner animation.
        </li>
    </ol>
    <p>
        Why disable the “Build” button? The
        <code>
            NSTask
        </code>
        will run each time the button is pressed, and as the app will be busy
        for an amount of time while the
        <code>
            NSTask
        </code>
        does its work, the user could impatiently press it many times — each time
        spawning a new build process. This action prevents the user from creating
        button click events while the app is busy.
    </p>
    <p>
        Build and run your application, then hit the
        <em>
            Build
        </em>
        button. You should see the “Build” button disable and the spinner animation
        start:
    </p>
    <p>
        <img src="https://koenig-media.raywenderlich.com/uploads/2016/02/busy1-536x500.png"
        alt="busy1" width="536" height="500" class="aligncenter size-large wp-image-126816"
        srcset="https://koenig-media.raywenderlich.com/uploads/2016/02/busy1-536x500.png 536w, https://koenig-media.raywenderlich.com/uploads/2016/02/busy1-343x320.png 343w, https://koenig-media.raywenderlich.com/uploads/2016/02/busy1-768x717.png 768w, https://koenig-media.raywenderlich.com/uploads/2016/02/busy1.png 870w"
        sizes="(max-width: 536px) 100vw, 536px">
    </p>
    <p>
        Your app
        <i>
            looks
        </i>
        pretty busy, but you know right now it’s not really doing anything. Time
        to add some
        <code>
            NSTask
        </code>
        magic.
    </p>
    <h2>
        Adding an NSTask to TasksProject
    </h2>
    <p>
        Open
        <em>
            TasksViewController.swift
        </em>
        and add the following method:
    </p>
    <div class="wp_codebox">
        <table>
            <tbody>
                <tr id="p1250713">
                    <td class="code" id="p125071code3">
                        <pre class="swift" style="font-family:monospace;">
                            <span style="color: #a61390;">
                                func
                            </span>
                            runScript
                            <span style="color: #002200;">
                                (
                            </span>
                            _ arguments
                            <span style="color: #002200;">
                                :
                            </span>
                            <span style="color: #002200;">
                                [
                            </span>
                            <span style="color: #a61390;">
                                String
                            </span>
                            <span style="color: #002200;">
                                ]
                            </span>
                            <span style="color: #002200;">
                                )
                            </span>
                            <span style="color: #002200;">
                                {
                            </span>
                            &nbsp;
                            <span style="color: #11740a; font-style: italic;">
                                //1.
                            </span>
                            isRunning
                            <span style="color: #002200;">
                                =
                            </span>
                            <span style="color: #a61390;">
                                true
                            </span>
                            &nbsp;
                            <span style="color: #11740a; font-style: italic;">
                                //2.
                            </span>
                            <span style="color: #a61390;">
                                let
                            </span>
                            taskQueue
                            <span style="color: #002200;">
                                =
                            </span>
                            DispatchQueue.global
                            <span style="color: #002200;">
                                (
                            </span>
                            qos
                            <span style="color: #002200;">
                                :
                            </span>
                            DispatchQoS.QoSClass.background
                            <span style="color: #002200;">
                                )
                            </span>
                            &nbsp;
                            <span style="color: #11740a; font-style: italic;">
                                //3.
                            </span>
                            taskQueue.async
                            <span style="color: #002200;">
                                {
                            </span>
                            &nbsp;
                            <span style="color: #11740a; font-style: italic;">
                                //TESTING CODE
                            </span>
                            &nbsp;
                            <span style="color: #11740a; font-style: italic;">
                                //4.
                            </span>
                            Thread.sleep
                            <span style="color: #002200;">
                                (
                            </span>
                            forTimeInterval
                            <span style="color: #002200;">
                                :
                            </span>
                            <span style="color: #2400d9;">
                                2.0
                            </span>
                            <span style="color: #002200;">
                                )
                            </span>
                            &nbsp;
                            <span style="color: #11740a; font-style: italic;">
                                //5.
                            </span>
                            DispatchQueue.main.async
                            <span style="color: #002200;">
                                (
                            </span>
                            execute
                            <span style="color: #002200;">
                                :
                            </span>
                            <span style="color: #002200;">
                                {
                            </span>
                            <span style="color: #a61390;">
                                self
                            </span>
                            .buildButton.isEnabled
                            <span style="color: #002200;">
                                =
                            </span>
                            <span style="color: #a61390;">
                                true
                            </span>
                            <span style="color: #a61390;">
                                self
                            </span>
                            .spinner.stopAnimation
                            <span style="color: #002200;">
                                (
                            </span>
                            <span style="color: #a61390;">
                                self
                            </span>
                            <span style="color: #002200;">
                                )
                            </span>
                            <span style="color: #a61390;">
                                self
                            </span>
                            .isRunning
                            <span style="color: #002200;">
                                =
                            </span>
                            <span style="color: #a61390;">
                                false
                            </span>
                            <span style="color: #002200;">
                                }
                            </span>
                            <span style="color: #002200;">
                                )
                            </span>
                            &nbsp;
                            <span style="color: #11740a; font-style: italic;">
                                //TESTING CODE
                            </span>
                            <span style="color: #002200;">
                                }
                            </span>
                            &nbsp;
                            <span style="color: #002200;">
                                }
                            </span>
                        </pre>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
    <p>
        If you look at the method step-by-step, you’ll see that the code does
        the following:
    </p>
    <ol>
        <li>
            Sets
            <code>
                isRunning
            </code>
            to
            <code>
                true
            </code>
            . This enables the
            <code>
                Stop
            </code>
            button, since it’s bound to the
            <code>
                TasksViewController
            </code>
            ‘s
            <code>
                isRunning
            </code>
            property via
            <a href="/?p=21752" sl-processed="1">
                Cocoa Bindings
            </a>
            . You want this to happen on the main thread.
        </li>
        <li>
            Creates a
            <code>
                DispatchQueue
            </code>
            to run the heavy lifting on a background thread.
        </li>
        <li>
            Uses
            <code>
                async
            </code>
            on the
            <code>
                DispatchQueue
            </code>
            The application will continue to process things like button clicks on
            the main thread, but the
            <code>
                NSTask
            </code>
            will run on the background thread until it is complete.
        </li>
        <li>
            This is a temporary line of code that causes the current thread to sleep
            for 2 seconds, simulating a long-running task.
        </li>
        <li>
            Once the job has finished, re-enables the
            <code>
                Build
            </code>
            button, stops the spinner animation, and sets
            <code>
                isRunning
            </code>
            to
            <code>
                false
            </code>
            which disables the “Stop” button. This needs to be done in the main thread,
            as you are manipulating UI elements.
        </li>
    </ol>
    <p>
        Now that you have a method that will run your task in a separate thread,
        you need to call it from somewhere in your app.
    </p>
    <p>
        Still in
        <em>
            TasksViewController.swift
        </em>
        , add the following code to the end of
        <code>
            startTask
        </code>
        just after
        <code>
            spinner.startAnimation(self)
        </code>
        :
    </p>
    <div class="wp_codebox">
        <table>
            <tbody>
                <tr id="p1250714">
                    <td class="code" id="p125071code4">
                        <pre class="swift" style="font-family:monospace;">
                            runScript
                            <span style="color: #002200;">
                                (
                            </span>
                            arguments
                            <span style="color: #002200;">
                                )
                            </span>
                        </pre>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
    <p>
        This calls
        <code>
            runScript
        </code>
        with the array of arguments you built in
        <code>
            startTask
        </code>
        .
    </p>
    <p>
        Build and run your application and hit the
        <em>
            Build
        </em>
        button. You’ll notice that the
        <em>
            Build
        </em>
        button will become disabled, the
        <code>
            Stop
        </code>
        button will become enabled and the spinner will start animating:
    </p>
    <p>
        <img src="https://koenig-media.raywenderlich.com/uploads/2016/02/busy2-536x500.png"
        alt="busy2" width="536" height="500" class="aligncenter size-large wp-image-126817"
        srcset="https://koenig-media.raywenderlich.com/uploads/2016/02/busy2-536x500.png 536w, https://koenig-media.raywenderlich.com/uploads/2016/02/busy2-343x320.png 343w, https://koenig-media.raywenderlich.com/uploads/2016/02/busy2-768x717.png 768w, https://koenig-media.raywenderlich.com/uploads/2016/02/busy2.png 870w"
        sizes="(max-width: 536px) 100vw, 536px">
    </p>
    <p>
        While the spinner is animating, you’ll still be able to interact with
        the application. Try it yourself — for example, you should be able to type
        in the
        <em>
            Target Name
        </em>
        field while the spinner is active.
    </p>
    <p>
        After two seconds have elapsed, the spinner will disappear,
        <em>
            Stop
        </em>
        will become disabled and
        <em>
            Build
        </em>
        will become enabled.
    </p>
    <div class="note">
        <p>
            <em>
                Note:
            </em>
            If you have trouble interacting with the application before it’s done
            sleeping, increase the number of seconds in your call to
            <code>
                sleep(forTimeInterval:)
            </code>
            .
        </p>
    </div>
    <p>
        Now that you’ve solved the UI responsiveness issues, you can finally implement
        your call to NSTask.
    </p>
    <div class="note">
        <p>
            <em>
                Note:
            </em>
            Swift calls the
            <code>
                NSTask
            </code>
            class by the name
            <code>
                Process
            </code>
            because of the
            <em>
                Foundation
            </em>
            framework stripping of the
            <em>
                NS
            </em>
            prefix in Swift 3. However you’ll read NSTask in this tutorial as thats
            going to be the most useful search term if you want to learn more.
        </p>
    </div>
    <p>
        In
        <em>
            TasksViewController.swift
        </em>
        , find the lines in
        <code>
            runScript
        </code>
        that are bracketed by the comment
        <code>
            //TESTING CODE
        </code>
        . Replace that entire section of code inside the
        <code>
            taskQueue.async
        </code>
        block with the following:
    </p>
    <div class="wp_codebox">
        <table>
            <tbody>
                <tr id="p1250715">
                    <td class="code" id="p125071code5">
                        <pre class="swift" style="font-family:monospace;">
                            <span style="color: #11740a; font-style: italic;">
                                //1.
                            </span>
                            guard
                            <span style="color: #a61390;">
                                let
                            </span>
                            path
                            <span style="color: #002200;">
                                =
                            </span>
                            Bundle.main.path
                            <span style="color: #002200;">
                                (
                            </span>
                            forResource
                            <span style="color: #002200;">
                                :
                            </span>
                            <span style="color: #bf1d1a;">
                                "BuildScript"
                            </span>
                            ,ofType
                            <span style="color: #002200;">
                                :
                            </span>
                            <span style="color: #bf1d1a;">
                                "command"
                            </span>
                            <span style="color: #002200;">
                                )
                            </span>
                            <span style="color: #a61390;">
                                else
                            </span>
                            <span style="color: #002200;">
                                {
                            </span>
                            <span style="color: #a61390;">
                                print
                            </span>
                            <span style="color: #002200;">
                                (
                            </span>
                            <span style="color: #bf1d1a;">
                                "Unable to locate BuildScript.command"
                            </span>
                            <span style="color: #002200;">
                                )
                            </span>
                            <span style="color: #a61390;">
                                return
                            </span>
                            <span style="color: #002200;">
                                }
                            </span>
                            &nbsp;
                            <span style="color: #11740a; font-style: italic;">
                                //2.
                            </span>
                            <span style="color: #a61390;">
                                self
                            </span>
                            .buildTask
                            <span style="color: #002200;">
                                =
                            </span>
                            Process
                            <span style="color: #002200;">
                                (
                            </span>
                            <span style="color: #002200;">
                                )
                            </span>
                            <span style="color: #a61390;">
                                self
                            </span>
                            .buildTask.launchPath
                            <span style="color: #002200;">
                                =
                            </span>
                            path
                            <span style="color: #a61390;">
                                self
                            </span>
                            .buildTask.arguments
                            <span style="color: #002200;">
                                =
                            </span>
                            arguments &nbsp;
                            <span style="color: #11740a; font-style: italic;">
                                //3.
                            </span>
                            <span style="color: #a61390;">
                                self
                            </span>
                            .buildTask.terminationHandler
                            <span style="color: #002200;">
                                =
                            </span>
                            <span style="color: #002200;">
                                {
                            </span>
                            &nbsp; task
                            <span style="color: #a61390;">
                                in
                            </span>
                            DispatchQueue.main.async
                            <span style="color: #002200;">
                                (
                            </span>
                            execute
                            <span style="color: #002200;">
                                :
                            </span>
                            <span style="color: #002200;">
                                {
                            </span>
                            <span style="color: #a61390;">
                                self
                            </span>
                            .buildButton.isEnabled
                            <span style="color: #002200;">
                                =
                            </span>
                            <span style="color: #a61390;">
                                true
                            </span>
                            <span style="color: #a61390;">
                                self
                            </span>
                            .spinner.stopAnimation
                            <span style="color: #002200;">
                                (
                            </span>
                            <span style="color: #a61390;">
                                self
                            </span>
                            <span style="color: #002200;">
                                )
                            </span>
                            <span style="color: #a61390;">
                                self
                            </span>
                            .isRunning
                            <span style="color: #002200;">
                                =
                            </span>
                            <span style="color: #a61390;">
                                false
                            </span>
                            <span style="color: #002200;">
                                }
                            </span>
                            <span style="color: #002200;">
                                )
                            </span>
                            &nbsp;
                            <span style="color: #002200;">
                                }
                            </span>
                            &nbsp;
                            <span style="color: #11740a; font-style: italic;">
                                //TODO - Output Handling
                            </span>
                            &nbsp;
                            <span style="color: #11740a; font-style: italic;">
                                //4.
                            </span>
                            <span style="color: #a61390;">
                                self
                            </span>
                            .buildTask.launch
                            <span style="color: #002200;">
                                (
                            </span>
                            <span style="color: #002200;">
                                )
                            </span>
                            &nbsp;
                            <span style="color: #11740a; font-style: italic;">
                                //5.
                            </span>
                            <span style="color: #a61390;">
                                self
                            </span>
                            .buildTask.waitUntilExit
                            <span style="color: #002200;">
                                (
                            </span>
                            <span style="color: #002200;">
                                )
                            </span>
                        </pre>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
    <p>
        The above code:
    </p>
    <ol>
        <li>
            Gets the path to a script named
            <code>
                BuildScript.command
            </code>
            , included in your application’s bundle. That script doesn’t exist right
            now — you’ll be adding it shortly.
        </li>
        <li>
            Creates a new
            <code>
                Process
            </code>
            object and assigns it to the
            <code>
                TasksViewController
            </code>
            ‘s
            <code>
                buildTask
            </code>
            property. The
            <code>
                launchPath
            </code>
            property is the path to the executable you want to run. Assigns the
            <code>
                BuildScript.command
            </code>
            ‘s
            <code>
                path
            </code>
            to the
            <code>
                Process
            </code>
            ‘s
            <code>
                launchPath
            </code>
            , then assigns the arguments that were passed to
            <code>
                runScript:
            </code>
            to
            <code>
                Process
            </code>
            ‘s
            <code>
                arguments
            </code>
            property.
            <code>
                Process
            </code>
            will pass the arguments to the executable, as though you had typed them
            into terminal.
        </li>
        <li>
            <code>
                Process
            </code>
            has a
            <code>
                terminationHandler
            </code>
            property that contains a block which is executed when the task is finished.
            This updates the UI to reflect that finished status as you did before.
        </li>
        <li>
            In order to run the task and execute the script, calls
            <code>
                launch
            </code>
            on the
            <code>
                Process
            </code>
            object. There are also methods to terminate, interrupt, suspend or resume
            an
            <code>
                Process
            </code>
            .
        </li>
        <li>
            Calls
            <code>
                waitUntilExit
            </code>
            , which tells the
            <code>
                Process
            </code>
            object to block any further activity on the current thread until the task
            is complete. Remember, this code is running on a background thread. Your
            UI, which is running on the main thread, will still respond to user input.
        </li>
    </ol>
    <p>
        Build and run your project; you won’t notice that anything
        <i>
            looks
        </i>
        different, but hit the
        <em>
            Build
        </em>
        button and check the output console. You should see an error like the
        following:
    </p>
    <div class="wp_codebox">
        <table>
            <tbody>
                <tr id="p1250716">
                    <td class="code" id="p125071code6">
                        <pre class="swift" style="font-family:monospace;">
                            Unable to locate BuildScript.command
                        </pre>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
    <p>
        This is the log from the guard statement at the start of the code you
        just added. Since you haven’t added the script yet, the
        <code>
            guard
        </code>
        is triggered.
    </p>
    <p>
        Looks like it’s time to write that script! :]
    </p>
    <h2>
        Writing a Build Shell Script
    </h2>
    <p>
        In Xcode, choose
        <em>
            File\New\File…
        </em>
        and select the
        <em>
            Other
        </em>
        category under
        <em>
            OS X
        </em>
        . Choose
        <em>
            Shell Script
        </em>
        and hit
        <em>
            Next
        </em>
        :
    </p>
    <p>
        <img src="https://koenig-media.raywenderlich.com/uploads/2016/04/add-script-1-650x463.png"
        alt="add script to project" width="650" height="463" class="aligncenter size-large wp-image-145431"
        srcset="https://koenig-media.raywenderlich.com/uploads/2016/04/add-script-1-650x463.png 650w, https://koenig-media.raywenderlich.com/uploads/2016/04/add-script-1-449x320.png 449w, https://koenig-media.raywenderlich.com/uploads/2016/04/add-script-1.png 737w"
        sizes="(max-width: 650px) 100vw, 650px">
    </p>
    <p>
        Name the file
        <em>
            BuildScript.command
        </em>
        . Before you hit
        <em>
            Create
        </em>
        , be sure
        <em>
            TasksProject
        </em>
        is selected under
        <em>
            Targets
        </em>
        , as shown below:
    </p>
    <p>
        <img src="https://koenig-media.raywenderlich.com/uploads/2016/02/target_choice-647x500.png"
        alt="target_choice" width="647" height="500" class="aligncenter size-large wp-image-126824"
        srcset="https://koenig-media.raywenderlich.com/uploads/2016/02/target_choice-647x500.png 647w, https://koenig-media.raywenderlich.com/uploads/2016/02/target_choice-414x320.png 414w, https://koenig-media.raywenderlich.com/uploads/2016/02/target_choice-768x593.png 768w, https://koenig-media.raywenderlich.com/uploads/2016/02/target_choice.png 852w"
        sizes="(max-width: 647px) 100vw, 647px">
    </p>
    <p>
        Open
        <em>
            BuildScript.command
        </em>
        and add the following commands at the end of the file:
    </p>
    <div class="wp_codebox">
        <table>
            <tbody>
                <tr id="p1250717">
                    <td class="code" id="p125071code7">
                        <pre class="objc" style="font-family:monospace;">
                            echo
                            <span style="color: #bf1d1a;">
                                "*********************************"
                            </span>
                            echo
                            <span style="color: #bf1d1a;">
                                "Build Started"
                            </span>
                            echo
                            <span style="color: #bf1d1a;">
                                "*********************************"
                            </span>
                            &nbsp; echo
                            <span style="color: #bf1d1a;">
                                "*********************************"
                            </span>
                            echo
                            <span style="color: #bf1d1a;">
                                "Beginning Build Process"
                            </span>
                            echo
                            <span style="color: #bf1d1a;">
                                "*********************************"
                            </span>
                            &nbsp; xcodebuild
                            <span style="color: #002200;">
                                -
                            </span>
                            project
                            <span style="color: #bf1d1a;">
                                "${1}"
                            </span>
                            <span style="color: #002200;">
                                -
                            </span>
                            target
                            <span style="color: #bf1d1a;">
                                "${2}"
                            </span>
                            <span style="color: #002200;">
                                -
                            </span>
                            sdk iphoneos
                            <span style="color: #002200;">
                                -
                            </span>
                            verbose CONFIGURATION_BUILD_DIR
                            <span style="color: #002200;">
                                =
                            </span>
                            <span style="color: #bf1d1a;">
                                "${3}"
                            </span>
                            &nbsp; echo
                            <span style="color: #bf1d1a;">
                                "*********************************"
                            </span>
                            echo
                            <span style="color: #bf1d1a;">
                                "Creating IPA"
                            </span>
                            echo
                            <span style="color: #bf1d1a;">
                                "*********************************"
                            </span>
                            &nbsp;
                            <span style="color: #002200;">
                                /
                            </span>
                            usr
                            <span style="color: #002200;">
                                /
                            </span>
                            bin
                            <span style="color: #002200;">
                                /
                            </span>
                            xcrun
                            <span style="color: #002200;">
                                -
                            </span>
                            verbose
                            <span style="color: #002200;">
                                -
                            </span>
                            sdk iphoneos PackageApplication
                            <span style="color: #002200;">
                                -
                            </span>
                            v
                            <span style="color: #bf1d1a;">
                                "${3}/${4}.app"
                            </span>
                            <span style="color: #002200;">
                                -
                            </span>
                            o
                            <span style="color: #bf1d1a;">
                                "${5}/app.ipa"
                            </span>
                        </pre>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
    <p>
        This is the entire build script that your
        <code>
            NSTask
        </code>
        calls.
    </p>
    <p>
        The
        <code>
            echo
        </code>
        commands that you see throughout your script will send whatever text is
        passed to them to
        <em>
            standard output
        </em>
        , which you capture as part of the return values from your
        <code>
            NSTask
        </code>
        object and display in your
        <code>
            outputText
        </code>
        field.
        <code>
            echo
        </code>
        statments are handy statements to let you know what your script is doing,
        since many commands don’t provide much, if any, output when run from the
        command line.
    </p>
    <p>
        You’ll notice that besides all of the
        <code>
            echo
        </code>
        commands, there are two other commands:
        <code>
            xcodebuild
        </code>
        , and
        <code>
            xcrun
        </code>
        .
    </p>
    <p>
        <em>
            xcodebuild
        </em>
        builds your application, creates an
        <code>
            .app
        </code>
        file, and places it in the subdirectory
        <em>
            /build
        </em>
        . Recall that you created an argument that references this directory way
        back in
        <code>
            startTask
        </code>
        , since you needed a place for the intermediate build files to live during
        the build and packaging process.
    </p>
    <p>
        <em>
            xcrun
        </em>
        runs the developer tools from the command line. Here you use it to call
        <code>
            PackageApplication
        </code>
        , which packages the
        <code>
            .app
        </code>
        file into an
        <code>
            .ipa
        </code>
        file. By setting the
        <code>
            verbose
        </code>
        flag, you’ll get a lot of details in the standard output, which you’ll
        be able to view in your
        <code>
            outputText
        </code>
        field.
    </p>
    <p>
        In both the
        <code>
            xcodebuild
        </code>
        and
        <code>
            xcrun
        </code>
        commands, you’ll notice that all of the arguments are written
        <code>
            “${1}”
        </code>
        instead of
        <code>
            $1
        </code>
        . This is because the paths to your projects may contain spaces. To handle
        that condition, you must wrap your file paths in quotes in order to get
        the right location. By putting the paths in quotes and curly braces, the
        script will properly parse the full path, spaces and all.
    </p>
    <p>
        What about the other parts of the script, the parts that Xcode automatically
        added for you? What do they mean?
    </p>
    <p>
        The first line of the script looks like this:
    </p>
    <div class="wp_codebox">
        <table>
            <tbody>
                <tr id="p1250718">
                    <td class="code" id="p125071code8">
                        <pre class="objc" style="font-family:monospace;">
                            <span style="color: #6e371a;">
                                #!/bin/sh
                            </span>
                        </pre>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
    <p>
        Although it looks like a comment since it’s prefixed with
        <code>
            #
        </code>
        , this line tells the operating system to use a specific
        <em>
            shell
        </em>
        when executing the remainder of the script. It’s called a
        <em>
            shebang
        </em>
        . The shell is the interpreter that runs your commands, either in script
        files or from a command line interface.
    </p>
    <p>
        There are many different shells available, but most of them adhere to
        some variation of either Bourne shell syntax or C shell syntax. Your script
        indicates that it should use
        <em>
            sh
        </em>
        , which is one of the shells included with OS X.
    </p>
    <p>
        If you wanted to specify another shell to execute your script, like
        <code>
            bash
        </code>
        , you would change the first line to contain the full path to the appropriate
        shell executable, like so:
    </p>
    <div class="wp_codebox">
        <table>
            <tbody>
                <tr id="p1250719">
                    <td class="code" id="p125071code9">
                        <pre class="objc" style="font-family:monospace;">
                            <span style="color: #6e371a;">
                                #!/bin/bash
                            </span>
                        </pre>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
    <p>
        In scripts, any argument you pass in is accessed by a
        <code>
            $
        </code>
        and a number.
        <code>
            $0
        </code>
        represents the name of the program you called, with all arguments after
        that referenced by
        <code>
            $1
        </code>
        ,
        <code>
            $2
        </code>
        and so forth.
    </p>
    <div class="note">
        <p>
            <em>
                Note:
            </em>
            Shell scripts have been around for about as long as computers, so you’ll
            find more information than you’ll ever want to know about them on the Internet.
            For a simple (and relevant) place to start, check out Apple’s
            <a href="https://developer.apple.com/library/mac/#documentation/opensource/conceptual/shellscripting/Introduction/Introduction.html"
            alt="Apple Shell Scripting Primer" sl-processed="1">
                Shell Scripting Primer
            </a>
            .
        </p>
    </div>
    <p>
        Now you’re ready to start calling your script from
        <code>
            NSTask
        </code>
        , right?
    </p>
    <p>
        Not quite. At this point, your script file doesn’t have
        <em>
            execute
        </em>
        permissions. That is, you can read and write the file, but you can’t execute
        it.
    </p>
    <p>
        This means if you build and run right now, your app will crash when you
        hit the Build button. Try it if you like. It’s not a big deal while developing,
        and you should see the exception “launch path not accessible” in your Xcode
        console.
    </p>
    <p>
        To make it executable, navigate to your project directory in Terminal.
        Terminal defaults to your Home directory, so if your project is in your
        <code>
            Documents
        </code>
        directory, you would type the command:
    </p>
    <div class="wp_codebox">
        <table>
            <tbody>
                <tr id="p12507110">
                    <td class="code" id="p125071code10">
                        <pre class="command" style="font-family:monospace;">
                            cd Documents/TasksProject
                        </pre>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
    <p>
        If your project is in another directory besides “Documents/TasksProject”,
        you’ll need to enter the correct path to your project folder. To do this
        quickly, click and drag your project folder from the Finder into Terminal.
        The path to your project will magically appear in the Terminal window!
        Now simply move your cursor to the front of that path, type
        <code>
            cd
        </code>
        followed by a space, and hit enter.
    </p>
    <p>
        To make sure you’re in the right place, type the following command into
        Terminal:
    </p>
    <div class="wp_codebox">
        <table>
            <tbody>
                <tr id="p12507111">
                    <td class="code" id="p125071code11">
                        <pre class="command" style="font-family:monospace;">
                            ls
                        </pre>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
    <p>
        Check that
        <code>
            BuildScript.command
        </code>
        in the file listing produced. If you’re not in the right place, check
        that you’ve correctly entered your project directory in Terminal.
    </p>
    <p>
        Once you’re assured that you’re in the correct directory, type the following
        command into Terminal:
    </p>
    <div class="wp_codebox">
        <table>
            <tbody>
                <tr id="p12507112">
                    <td class="code" id="p125071code12">
                        <pre class="command" style="font-family:monospace;">
                            chmod +x BuildScript.command
                        </pre>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
    <p>
        The
        <code>
            chmod
        </code>
        command changes the permissions of the script to allow it to be executed
        by your
        <code>
            NSTask
        </code>
        object. If you try to run your application without these permissions in
        place, you’d see the same “Launch path not accessible” error as before.
        You only need to do this once for each new script that you add to your
        project.
    </p>
    <div class="note">
        <p>
            <em>
                Note:
            </em>
            Using scripts like this is simple if you are developing for yourself or
            shipping your app outside the Mac App Store (MAS), however when developing
            for the MAS the sandbox rules that apply to your app are inherited by your
            scripts and you’ll need to use more complex techniques to use command line
            programs. These techniques are beyond the scope of this tutorial. See the
            links at the end for more details.
        </p>
    </div>
    <p>
        Clean and run your project; the “clean” is necessary as Xcode won’t pick
        up on the file’s permissions change, and therefore won’t copy it into the
        build repository. Once the application opens up, type in the target name
        of your test app, ensure the “Project Location” and “Build Repository”
        values are set correctly, and finally hit
        <em>
            Build
        </em>
        .
    </p>
    <p>
        When the spinner disappears, you should have a new
        <code>
            .ipa
        </code>
        file in your desired location. Success!
    </p>
    <h2>
        Using Outputs
    </h2>
    <p>
        Okay, you’re pretty well versed in passing arguments to command line programs,
        but what about dealing with the output of these command line programs?
    </p>
    <p>
        To see this in action, type the following command into Terminal and hit
        Enter:
    </p>
    <div class="wp_codebox">
        <table>
            <tbody>
                <tr id="p12507113">
                    <td class="code" id="p125071code13">
                        <pre class="command" style="font-family:monospace;">
                            date
                        </pre>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
    <p>
        You should see a message produced that looks something like this:
    </p>
    <div class="wp_codebox">
        <table>
            <tbody>
                <tr id="p12507114">
                    <td class="code" id="p125071code14">
                        <pre class="command" style="font-family:monospace;">
                            Fri 19 Feb 2016 17:48:15 GMT
                        </pre>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
    <p>
        <code>
            date
        </code>
        tells you the current date and time. Although it isn’t immediately obvious,
        the results were sent back to you on a channel called
        <em>
            standard output
        </em>
        .
    </p>
    <p>
        Processes generally have three default channels for input and output:
    </p>
    <ul>
        <li>
            <em>
                standard input
            </em>
            , which accepts input from the caller;
        </li>
        <li>
            <em>
                standard output
            </em>
            , which sends output from the process back to the caller; and
        </li>
        <li>
            <em>
                standard error
            </em>
            , which sends errors from the process back to the caller.
        </li>
    </ul>
    <p>
        Pro tip: you’ll see these commonly abbreviated as
        <em>
            stdin
        </em>
        ,
        <em>
            stdout
        </em>
        , and
        <em>
            stderr
        </em>
        .
    </p>
    <p>
        There is also a
        <code>
            pipe
        </code>
        that allows you to redirect the output of one process into the input of
        another process. You’ll be creating a pipe to let your application see
        the standard output from the process that
        <code>
            NSTask
        </code>
        runs.
    </p>
    <p>
        To see a pipe in action, ensure the volume is turned up on your computer,
        then type the following command in Terminal:
    </p>
    <div class="wp_codebox">
        <table>
            <tbody>
                <tr id="p12507115">
                    <td class="code" id="p125071code15">
                        <pre class="command" style="font-family:monospace;">
                            date | say
                        </pre>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
    <p>
        Hit enter and you should hear your computer telling you what time it is.
    </p>
    <div class="note">
        <p>
            <em>
                Note:
            </em>
            The pipe character “|” on your keyboard is usually located on the back
            slash
            <code>
                \
            </code>
            key, just above the
            <code>
                enter/return
            </code>
            key.
        </p>
    </div>
    <p>
        Here’s what just happened: you created a pipe that takes the standard
        output of
        <code>
            date
        </code>
        and redirects it into the standard input of
        <code>
            say
        </code>
        . You can also provide options to the commands that communicate with pipes,
        so if you would like to hear the date with an Australian accent, type the
        following command instead:
    </p>
    <div class="wp_codebox">
        <table>
            <tbody>
                <tr id="p12507116">
                    <td class="code" id="p125071code16">
                        <pre class="command" style="font-family:monospace;">
                            date | say -v karen
                        </pre>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
    <p>
        Its the date, mate!
    </p>
    <p>
        You can construct some rather long chains of commands using pipes, redirecting
        the stdout from one command into the stdin of another. Once you get comfortable
        using stdin, stdout, and pipe redirects, you can do some really complicated
        things from the command line using tools like pipes.
    </p>
    <p>
        Now it’s time to implement a pipe in your app.
    </p>
    <div class="note">
        <p>
            <em>
                Note:
            </em>
            <code>
                NSPipe
            </code>
            is a
            <em>
                Foundation
            </em>
            class that is called
            <code>
                Pipe
            </code>
            in Swift 3. Most documentation will refer to
            <code>
                NSPipe
            </code>
            .
        </p>
    </div>
    <p>
        Open
        <em>
            TasksViewController.swift
        </em>
        and replace the comment that reads
        <code>
            // TODO: Output Handling
        </code>
        in
        <code>
            runScript(_:)
        </code>
        with the following code:
    </p>
    <div class="wp_codebox">
        <table>
            <tbody>
                <tr id="p12507117">
                    <td class="code" id="p125071code17">
                        <pre class="swift" style="font-family:monospace;">
                            <span style="color: #a61390;">
                                self
                            </span>
                            .captureStandardOutputAndRouteToTextView
                            <span style="color: #002200;">
                                (
                            </span>
                            <span style="color: #a61390;">
                                self
                            </span>
                            .buildTask
                            <span style="color: #002200;">
                                )
                            </span>
                        </pre>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
    <p>
        Next, add this function to
        <em>
            TasksViewController.swift
        </em>
        :
    </p>
    <div class="wp_codebox">
        <table>
            <tbody>
                <tr id="p12507118">
                    <td class="code" id="p125071code18">
                        <pre class="swift" style="font-family:monospace;">
                            <span style="color: #a61390;">
                                func
                            </span>
                            captureStandardOutputAndRouteToTextView
                            <span style="color: #002200;">
                                (
                            </span>
                            _ task
                            <span style="color: #002200;">
                                :
                            </span>
                            Process
                            <span style="color: #002200;">
                                )
                            </span>
                            <span style="color: #002200;">
                                {
                            </span>
                            &nbsp;
                            <span style="color: #11740a; font-style: italic;">
                                //1.
                            </span>
                            outputPipe
                            <span style="color: #002200;">
                                =
                            </span>
                            Pipe
                            <span style="color: #002200;">
                                (
                            </span>
                            <span style="color: #002200;">
                                )
                            </span>
                            task.standardOutput
                            <span style="color: #002200;">
                                =
                            </span>
                            outputPipe &nbsp;
                            <span style="color: #11740a; font-style: italic;">
                                //2.
                            </span>
                            outputPipe.fileHandleForReading.waitForDataInBackgroundAndNotify
                            <span style="color: #002200;">
                                (
                            </span>
                            <span style="color: #002200;">
                                )
                            </span>
                            &nbsp;
                            <span style="color: #11740a; font-style: italic;">
                                //3.
                            </span>
                            NotificationCenter.
                            <span style="color: #a61390;">
                                default
                            </span>
                            .addObserver
                            <span style="color: #002200;">
                                (
                            </span>
                            forName
                            <span style="color: #002200;">
                                :
                            </span>
                            <span style="color: #400080;">
                                NSNotification
                            </span>
                            .Name.NSFileHandleDataAvailable, object
                            <span style="color: #002200;">
                                :
                            </span>
                            outputPipe.fileHandleForReading , queue
                            <span style="color: #002200;">
                                :
                            </span>
                            <span style="color: #a61390;">
                                nil
                            </span>
                            <span style="color: #002200;">
                                )
                            </span>
                            <span style="color: #002200;">
                                {
                            </span>
                            notification
                            <span style="color: #a61390;">
                                in
                            </span>
                            &nbsp;
                            <span style="color: #11740a; font-style: italic;">
                                //4.
                            </span>
                            <span style="color: #a61390;">
                                let
                            </span>
                            output
                            <span style="color: #002200;">
                                =
                            </span>
                            <span style="color: #a61390;">
                                self
                            </span>
                            .outputPipe.fileHandleForReading.availableData
                            <span style="color: #a61390;">
                                let
                            </span>
                            outputString
                            <span style="color: #002200;">
                                =
                            </span>
                            <span style="color: #a61390;">
                                String
                            </span>
                            <span style="color: #002200;">
                                (
                            </span>
                            data
                            <span style="color: #002200;">
                                :
                            </span>
                            output, encoding
                            <span style="color: #002200;">
                                :
                            </span>
                            <span style="color: #a61390;">
                                String
                            </span>
                            .Encoding.utf8
                            <span style="color: #002200;">
                                )
                            </span>
                            ??
                            <span style="color: #bf1d1a;">
                                ""
                            </span>
                            &nbsp;
                            <span style="color: #11740a; font-style: italic;">
                                //5.
                            </span>
                            DispatchQueue.main.async
                            <span style="color: #002200;">
                                (
                            </span>
                            execute
                            <span style="color: #002200;">
                                :
                            </span>
                            <span style="color: #002200;">
                                {
                            </span>
                            <span style="color: #a61390;">
                                let
                            </span>
                            previousOutput
                            <span style="color: #002200;">
                                =
                            </span>
                            <span style="color: #a61390;">
                                self
                            </span>
                            .outputText.string ??
                            <span style="color: #bf1d1a;">
                                ""
                            </span>
                            <span style="color: #a61390;">
                                let
                            </span>
                            nextOutput
                            <span style="color: #002200;">
                                =
                            </span>
                            previousOutput
                            <span style="color: #002200;">
                                +
                            </span>
                            <span style="color: #bf1d1a;">
                                "
                                <span style="color: #2400d9;">
                                    \n
                                </span>
                                "
                            </span>
                            <span style="color: #002200;">
                                +
                            </span>
                            outputString
                            <span style="color: #a61390;">
                                self
                            </span>
                            .outputText.string
                            <span style="color: #002200;">
                                =
                            </span>
                            nextOutput &nbsp;
                            <span style="color: #a61390;">
                                let
                            </span>
                            range
                            <span style="color: #002200;">
                                =
                            </span>
                            <span style="color: #400080;">
                                NSRange
                            </span>
                            <span style="color: #002200;">
                                (
                            </span>
                            location
                            <span style="color: #002200;">
                                :
                            </span>
                            nextOutput.characters.
                            <span style="color: #a61390;">
                                count
                            </span>
                            ,length
                            <span style="color: #002200;">
                                :
                            </span>
                            <span style="color: #2400d9;">
                                0
                            </span>
                            <span style="color: #002200;">
                                )
                            </span>
                            <span style="color: #a61390;">
                                self
                            </span>
                            .outputText.scrollRangeToVisible
                            <span style="color: #002200;">
                                (
                            </span>
                            range
                            <span style="color: #002200;">
                                )
                            </span>
                            &nbsp;
                            <span style="color: #002200;">
                                }
                            </span>
                            <span style="color: #002200;">
                                )
                            </span>
                            &nbsp;
                            <span style="color: #11740a; font-style: italic;">
                                //6.
                            </span>
                            <span style="color: #a61390;">
                                self
                            </span>
                            .outputPipe.fileHandleForReading.waitForDataInBackgroundAndNotify
                            <span style="color: #002200;">
                                (
                            </span>
                            <span style="color: #002200;">
                                )
                            </span>
                            <span style="color: #002200;">
                                }
                            </span>
                            &nbsp;
                            <span style="color: #002200;">
                                }
                            </span>
                        </pre>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
    <p>
        This function collects the output from the external process and adds it
        to the GUI’s
        <code>
            outputText
        </code>
        field. It works as follows:
    </p>
    <ol>
        <li>
            Creates an
            <code>
                Pipe
            </code>
            and attaches it to
            <code>
                buildTask
            </code>
            ‘s standard output.
            <code>
                Pipe
            </code>
            is a class representing the same kind of pipe that you created in
            <code>
                Terminal
            </code>
            . Anything that is written to
            <code>
                buildTask
            </code>
            ‘s
            <code>
                stdout
            </code>
            will be provided to this
            <code>
                Pipe
            </code>
            object.
        </li>
        <li>
            <code>
                Pipe
            </code>
            has two properties:
            <code>
                fileHandleForReading
            </code>
            and
            <code>
                fileHandleForWriting
            </code>
            . These are
            <code>
                NSFileHandle
            </code>
            objects. Covering
            <code>
                NSFileHandle
            </code>
            is beyond the scope of this tutorial, but the
            <code>
                fileHandleForReading
            </code>
            is used to read the data in the pipe. You call
            <code>
                waitForDataInBackgroundAndNotify
            </code>
            on it to use a separate background thread to check for available data.
        </li>
        <li>
            Whenever data is available,
            <code>
                waitForDataInBackgroundAndNotify
            </code>
            notifies you by calling the block of code you register with
            <code>
                NSNotificationCenter
            </code>
            to handle
            <code>
                NSFileHandleDataAvailableNotification
            </code>
            .
        </li>
        <li>
            Inside your notification handler, gets the data as an
            <code>
                NSData
            </code>
            object and converts it to a string.
        </li>
        <li>
            On the main thread, appends the string from the previous step to the end
            of the text in
            <code>
                outputText
            </code>
            and scrolls the text area so that the user can see the latest output as
            it arrives. This
            <i>
                must
            </i>
            be on the main thread, like all UI and user interaction.
        </li>
        <li>
            Finally, repeats the call to wait for data in the background. This creates
            a loop that will continually wait for available data, process that data,
            wait for available data, and so on.
        </li>
    </ol>
    <p>
        Build and run your application again; make sure the
        <code>
            Project Location
        </code>
        and
        <code>
            Build Repository
        </code>
        fields are set correctly, type in your target’s name and click
        <em>
            Build
        </em>
        .
    </p>
    <p>
        You should see the output from the building process in your
        <code>
            outputText
        </code>
        field:
    </p>
    <p>
        <img src="https://koenig-media.raywenderlich.com/uploads/2016/04/final-window-490x500.png"
        alt="final window view" width="490" height="500" class="aligncenter size-large wp-image-145429"
        srcset="https://koenig-media.raywenderlich.com/uploads/2016/04/final-window-490x500.png 490w, https://koenig-media.raywenderlich.com/uploads/2016/04/final-window-314x320.png 314w, https://koenig-media.raywenderlich.com/uploads/2016/04/final-window-32x32.png 32w, https://koenig-media.raywenderlich.com/uploads/2016/04/final-window-50x50.png 50w, https://koenig-media.raywenderlich.com/uploads/2016/04/final-window-64x64.png 64w, https://koenig-media.raywenderlich.com/uploads/2016/04/final-window.png 513w"
        sizes="(max-width: 490px) 100vw, 490px">
    </p>
    <h2>
        Stopping an NSTask
    </h2>
    <p>
        What happens if you start a build, then change your mind? What if it’s
        taking too long, or something else seems to have gone wrong and it’s just
        hanging there, making no progress? These are times when you’ll want to
        be able to stop your background task. Fortunately, this is pretty easy
        to do.
    </p>
    <p>
        In
        <em>
            TasksViewController.swift
        </em>
        , add the following code to
        <code>
            stopTask(_:)
        </code>
    </p>
    <div class="wp_codebox">
        <table>
            <tbody>
                <tr id="p12507119">
                    <td class="code" id="p125071code19">
                        <pre class="swift" style="font-family:monospace;">
                            <span style="color: #a61390;">
                                if
                            </span>
                            isRunning
                            <span style="color: #002200;">
                                {
                            </span>
                            buildTask.terminate
                            <span style="color: #002200;">
                                (
                            </span>
                            <span style="color: #002200;">
                                )
                            </span>
                            <span style="color: #002200;">
                                }
                            </span>
                        </pre>
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
    <p>
        The code above simply checks if the
        <code>
            NSTask
        </code>
        is running, and if so, calls its
        <code>
            terminate
        </code>
        method. This will stop the
        <code>
            NSTask
        </code>
        in its tracks.
    </p>
    <p>
        Build and run your app, ensure all fields are configured correctly and
        hit the
        <em>
            Build
        </em>
        button. Then hit the
        <em>
            Stop
        </em>
        button before the build is complete. You’ll see that everything stops
        and no new
        <code>
            .ipa
        </code>
        file is created in your output directory.
    </p>
    <h2>
        Where to Go From Here?
    </h2>
    <div class="inline-video-ad" id="sub-banner-inline">
        <div class="inline-video-ad-wrapper">
            <a href="https://videos.raywenderlich.com/courses" sl-processed="1">
                <div class="col-wrapper">
                    <div class="col">
                        <img src="https://cdn3.raywenderlich.com/wp-content/themes/raywenderlich/images/global/video-yeti@2x.png"
                        alt="yeti holding videos">
                    </div>
                    <div class="col large-col">
                        <span>
                            Want to learn even faster? Save time with our
                            <span>
                                video courses
                            </span>
                        </span>
                    </div>
                </div>
            </a>
        </div>
    </div>
    <p>
        Here is the finished
        <a href="https://koenig-media.raywenderlich.com/uploads/2016/04/TasksProject-completed_s3.zip"
        sl-processed="1">
            NSTask example project
        </a>
        from the above tutorial.
    </p>
    <p>
        Congratulations, you’ve begun the process of becoming an
        <code>
            NSTask
        </code>
        ninja!
    </p>
    <p>
        In one short tutorial, you’ve learned:
    </p>
    <ul>
        <li>
            How to create
            <code>
                NSTasks
            </code>
            with arguments and output pipes; and
        </li>
        <li>
            How to create a shell script and call it from your app!
        </li>
    </ul>
    <p>
        To learn more about
        <code>
            NSTask
        </code>
        , check out Apple’s official
        <a href="https://developer.apple.com/library/mac/#documentation/cocoa/Reference/Foundation/Classes/NSTask_Class/Reference/Reference.html"
        sl-processed="1">
            NSTask Class Reference
        </a>
        .
    </p>
    <p>
        To learn about using command line programs in a sandboxed app see
        <a href="https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/Introduction.html#//apple_ref/doc/uid/10000172i"
        sl-processed="1">
            Daemons and Services Programming Guide
        </a>
        and
        <a href="https://developer.apple.com/library/mac/documentation/System/Reference/XPCServicesFW/index.html#//apple_ref/doc/uid/TP40010357"
        sl-processed="1">
            XPC Services API Reference
        </a>
        .
    </p>
    <p>
        This tutorial only dealt with working with stdout with NSTask, but you
        can use
        <a href="http://en.wikipedia.org/wiki/Standard_streams" sl-processed="1">
            stdin and stderr
        </a>
        as well! To practice your new skills, try working with these.
    </p>
    <p>
        I hope you enjoyed this
        <code>
            NSTask
        </code>
        tutorial and that you find it useful in your future Mac OS X apps. If
        you have any questions or comments, please join the forum discussion below!
    </p>
</div>