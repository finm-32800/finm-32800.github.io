# Env Files, Secrets, and the Separations of Settings from Code

## The Twelve-Factor App methodology

The [Twelve-Factor App](https://12factor.net/) methodology is a set of best practices designed to enable applications to be built with portability and resilience when deployed to the web. The methodology draws inspiration from Martin Fowler’s books on software development practices, aiming to provide a shared vocabulary and conceptual solutions to common problems in modern application development. It [was created by developers at Heroku](https://en.wikipedia.org/wiki/Twelve-Factor_App_methodology), a platform-as-a-service company. This methodology synthesizes the experiences and observations of its creators in developing and deploying a hundreds of applications, particularly on the Heroku platform. While many of these principles apply specifically to web apps and "software-as-a-service", many of the principles apply more universally to software development. Let's take a brief look at the 12 principles. Then, we'll discuss the first three in a little more detail, as they directly apply to us today.

The Twelve Factors:
1. **Codebase**: One codebase tracked in revision control, many deploys.
2. **Dependencies**: Explicitly declare and isolate dependencies.
3. **Config**: Store config in the environment.
4. **Backing Services**: Treat backing services as attached resources.
    - "A backing service is any service the app consumes over the network as part of its normal operation. Examples include datastores (such as MySQL or CouchDB)...Backing services like the database are traditionally managed by the same systems administrators who deploy the app’s runtime. The code for a twelve-factor app makes no distinction between local and third party services. To the app, both are attached resources, accessed via a URL or other locator/credentials stored in the config. A deploy of the twelve-factor app should be able to swap out a local MySQL database with one managed by a third party (such as Amazon RDS) without any changes to the app’s code."
5. **Build, Release, Run**: Strictly separate build and run stages.
6. **Processes**: Execute the app as one or more stateless processes.
7. **Port Binding**: Export services via port binding.
8. **Concurrency**: Scale out via the process model.
9. **Disposability**: Maximize robustness with fast startup and graceful shutdown.
10. **Dev/Prod Parity**: Keep development, staging, and production as similar as possible.
11. **Logs**: Treat logs as event streams.
12. **Admin Processes**: Run admin/management tasks as one-off processes.

Now, consider the first three principles. Over the last 2 weeks, we have learned to incorporate the first two factors into our code. Today we discuss the third: storing config in the environment.

## Storing Config in Environment Variables

Please refer to [Section 3 of the Twelve-Factor App](https://12factor.net/config). Here is a snippet:

> An app’s config is everything that is likely to vary between deploys (staging, production, developer environments, etc). This includes:
> 
> - Resource handles to the database, Memcached, and other backing services
> - Credentials to external services such as Amazon S3 or Twitter
> - Per-deploy values such as the canonical hostname for the deploy
>
> Apps sometimes store config as constants in the code. This is a violation of twelve-factor, which requires strict separation of config from code. Config varies substantially across deploys, code does not.
> ...
> 
> The twelve-factor app stores config in environment variables (often shortened to env vars or env). Env vars are easy to change between deploys without changing any code; unlike config files, there is little chance of them being checked into the code repo accidentally; and unlike custom config files, or other config mechanisms such as Java System Properties, they are a language- and OS-agnostic standard.

## What is a Dot Env (`.env`) File?

A `.env` file, short for "environment file", is a simple text file containing environment variables. These variables are key-value pairs that can store configuration settings and other data that should not be hard-coded directly into source code. This practice is particularly important for security, maintainability, and the flexibility of the software.

 - **Format**: Typically, each line in a `.env` file contains a single environment variable expressed as `KEY=value`. For example, `OUTPUT_DIR=./data/` or `API_KEY=yourkeyhere`.

```
# Example .env file
DATA_DIR="D:/Dropbox/project_data/blank_project"
OUTPUT_DIR="C:/Users/jdoe/GitRepositories/blank_project/output"
WRDS_USERNAME="jdoe"
```

- **Secrets**: These files may often  contain sensitive information, like API keys, database passwords, and service credentials, which should not be exposed publicly.
- **Version Control**: `.env` should NOT be checked into version control systems (like Git). The point is to keep sensitive data secure. Instead, a template (like `env.example`) with dummy values can be added to the repository to guide setup. You'll see this in my [`blank_project` repo](https://github.com/jmbejara/blank_project)
- **Separation of Configurations and Environment-Specific Settings**: By using `.env` files, you can separate configuration from code. They allow for different settings across environments (development, testing, production), without changing the code. I often use this to specify data and output directories that may change based on the environment that I'm using at the moment to run my code. I also use this when your HW runs on the autograder. That way, your code doesn't need to change to accommodate the different runtime environment used by the autograder.
- **Loading Environment Variables**: In most programming languages, there are libraries or built-in functionalities to load these variables into the application's environment. For instance, in Python, the `python-dotenv` or the `decouple` packages can be used to load variables from a `.env` file. Furthermore, environment variables are mostly agnostic to the platform (.e.g, Windows, Linux, or MacOS).

- **Accessing Variables**: Once loaded, these variables can be accessed just like any other environment variable in your code. For example, in Python, you would use `os.environ.get('KEY')` to access the value of `KEY`.

- **Dynamic Configuration**: This approach allows the application to be configured dynamically based on the environment it's running in. This is crucial for applications that need to run in multiple environments with different configurations (like database URLs, logging levels, etc.).

Recapping **Best Practices**

- **Keep it Secure**: Never commit `.env` files to version control. Always use `.gitignore` or equivalent in other version control systems to exclude this file.  
- **Documentation**: Provide documentation or an example `.env` file to guide setting up the necessary environment variables.
- **Validation and Defaults**: Implement validation for environment variables in your application and set sensible defaults where appropriate.

## Examples and Demos

For practical examples and demonstrations of using environment variables in Python, please refer to the in-class examples repository:

**[Environment Variables Examples](https://github.com/finm-32800/inclass_examples/tree/main/env_vars)**

This repository contains complete working examples that demonstrate:

- Setting up and using `.env` files
- The precedence of environment variables (local, global, `.env`, and default values)
- Using Python's `decouple` and `dotenv` packages
- Cross-platform configuration management (Windows, Linux, MacOS)

The examples in the repository serve as the source of truth for how to properly implement environment variables in your projects.

## Python's `decouple` and `dotenv` packages:

For additional reference, review the official documentation:

 - https://pypi.org/project/python-decouple/
 - https://pypi.org/project/python-dotenv/