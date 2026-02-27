## GSoC 2026 project ideas

We have listed some tasks we extracted from our prioritized feature requests. Discuss
your own ideas with us via [Discord](https://discord.gg/cybpp4guTJ) or email (`neutralinojs[AT]gmail.com`) or create a GitHub discussion thread. You can contribute to the Neutralinojs framework, CLI, client library, and templates with your innovative ideas.

Thank you for contributing to open-source 🎉

 _[Click here](./README.md) to go back to the GSoC getting started guide._

### Neutralinojs builder: a CLI plugin to generate platform-specific app installers

Neutralinojs CLI generates platform-specific binaries for Linux, macOS, and Windows with a platform-independent resource file. Neutralinojs application developers currently should use various tools to create application installers (i.e., AppImage, NSIS) for each operating system. However, we have no plans to add application installer generation support directly to the neu CLI codebase to keep the CLI implementation minimal and less platform-dependent. So, we would like to implement the platform-specific installer generator as a plugin for the official CLI within the official Neutralinojs GitHub organization.

Skills required: Node.js, Neutralinojs, Application bundling on operating systems

Difficulty rating: Medium

Project size: ~350h

Mentors: Shalitha Suranga, Athif Shaffy, and Sajath Ahamed

#### Suggested technical decisions

- Developing a CLI plugin for the solution.
- Expose a new command to generate application packages.

```bash
# Installing the plugin
neu plugins --add neutralinojs-builder

# neu builder <target> <arch>
neu builder nsis --x64 # NSIS setup for Windows x64
neu builder deb --ia32 # Debian package for GNU/Linux ia32
neu builder appimage --x64 # AppImage for GNU/Linux x64
neu builder deb # GNU/Linux Debian packages for all supported CPU architectures

# Use configuration from neutralino.config.json
neu builder

# Removing the plugin
neu plugins --remove neutralinojs-builder
```
- If the developer runs `neu builder` without any parameters, get the targets from the config file:

```json
"cli": {
  "builder": {
    "linux": {
      "targets": [
        {
          "target": "deb",
          "arch": [
            "x64",
            "ia32",
            "armhf"
          ]
        }
      ]
    },
    "win": {
      "targets": [
        {
          "target": "nsis",
          "arch": [
            "x64",
            "ia32"
          ]
        }
      ]
    }
  }
}
```
- Implement package targets as internal plugins (import only required modules based on targets). Try to use modules like `targets/deb.js`, `targets/nsis.js` for dynamic loading.
- Keep the codebase minimal by following design patterns and principles that the official neu CLI project uses.
- Use the neu CLI core APIs from the plugin and avoid repetitive code between the neu CLI and Neutralinojs builder projects.
- Recommend users to install the builder plugin from the neu CLI if they need app installers

### Rendering a native loading animation before loading the app

Neutralinojs renders the frontend web content of apps using platform-specific webview components without using a loading animation. The current implementation doesn't create any issues for small app frontends, but large frontends render a blank white screen at startup for a short period, affecting the software quality and usability. The blank white screen appears for a considerable time in low-end devices and when developers load remote URLs. The only workaround for this issue is hiding and showing the app when it's ready, but it slows down the initial visible rendering time for users. This project idea suggests implementing a native loading animation for all supported platforms as a default feature to fix the startup white screen issue.

Related issue: https://github.com/neutralinojs/neutralinojs/issues/814

Skills required: C++, Neutralinojs, platform-specific GUI development frameworks (GTK, Windows API, and Cocoa)

Difficulty rating: Medium

Project size: ~350h

Mentors: Shalitha Suranga, Athif Shaffy, and Sajath Ahamed

#### Suggested UI/UX decisions

- Use platform-specific spinner or infinite progress bar controls
- Allow developers to use a custom GIF animation or a static image
- Center the loading animation within the app window
- Use proper background and foreground colors based on the current system theme
- Indicate the loading state in the mouse cursor by using an appropriate built-in icon

#### Suggested technical decisions

- Use native, built-in GUI controls in each operating system for the default loader
- Let developers use a GIF from app resources and override the default loader setting
- Keep the implementation only within the C++ webview library codebase fork
- Use the following configuration block in `neutralino.config.json`:

```js
"window": {
  "startupLoader": {
    "type": "image", // none, system (default), image
    "image": "/resources/images/loader.gif"
  }
}
```

### Extending the existing native API with essential functions 

Neutralinojs offers a well-structured, cross-platform native API for app developers. The current native API offers many JavaScript functions under several namespaces that most app developers can use for building general cross-platform apps. However, the current native API doesn't offer solutions for every specific development scenario -- app developers sometimes have to write native extensions or implement platform-specific command-line solutions as workarounds to implement several features that the framework itself can embed. For example, there are no built-in APIs to retrieve network details, handle file permissions, etc. This project idea suggests that contributors conduct research for such missing framework features and implement them within the framework codebase.

Related issues: [neutralinojs/neutralinojs#issues](https://github.com/neutralinojs/neutralinojs/issues?q=is%3Aissue%20state%3Aopen%20label%3AAPI%20label%3Afeature-request)

Skills required: C++, JavaScript, Neutralinojs, platform-specific native APIs (POSIX and Windows APIs)

Difficulty rating: Medium

Project size: ~350h

Mentors: Shalitha Suranga, Athif Shaffy, and Sajath Ahamed

#### Research ideas

- Evaluate existing GitHub issues and discussions
- Finding missing functions by comparing Neutralinojs with other frameworks and built-in Node.js APIs
- Search forum threads created by mentioning missing native APIs in Neutralinojs
- Prepare a list of functions that can be added to the framework without heavily affecting bundle size, performance, and code complexity

#### Suggested technical decisions

- Implement new function names, parameters, and return values strictly adhering to the existing native API design
- Search for minimal, header-only libraries if the C++ standard library doesn't offer a solution
- Implement functions in a way that eliminates security vulnerabilities
