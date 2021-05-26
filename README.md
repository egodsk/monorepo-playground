# monorepo-playground

This purpose of this repository is to look into the different options available for a monorepository for Typescript projects.

## Goals

- Allow individual packages to be independent in their build and way of working
- Enable easy and convenient local development
- Enable sharing of code via packages
- Allow packages to build using their own preferred method

## Experiments

Below different experiments will be described to show the different tested methods, discussing some strengths and known weaknesses.

### Lerna

Lerna allows the individual developers to locally develop independent packages using all and any of their shared packages by creating automatic symlinking using `lerna bootstrap`

Using lerna allows for packages to build their packages independently and achieves the correct order of builds between packages with internal dependencies

Lerna however introduces the concept of phantom dependencies, as lerna is installed as a local dependency introducing a top-level layer of `node_modules` which might introduce packages other packages will be able to use, without being installed as part of their own `package.json` file.

Lerna allows the usage of individual package versioning, and only requires the bump of versioning of different packages in the event that common shared packages include changes to other packages. Lerna however allows easy management of this using `lerna version` which handles looking at the dependency graph between packages, and updating their individual version based on these changes.

Lerna allows each individual package to individually handle their own code as well as how source code is built and shared between other packages. This allows e.g. one package to use `webpack` to build their solution, while another uses the `tsc`

To allow correct IDE support and enable `Go to definition` the usage of typescript path references is used, only for the IDE. When the actual building of the projects are done, these are discarded, as any dependency should be handled via packages defined in the package.json files, and symlinked via lerna.

#### Running

Using the monorepo using Lerna the following commands can be run (all of these should be run from the root directory):

- `npm run bootstrap`: Setup the project, doing any linking of node_modules necessary between local packages. This command can be run whenever a new packages are added, or a new clone is done.
- `npm run watch --scopes=foo,bar`: Enable watching for changes independently inside each package. Will automatically start watching dependents of the projects. Requires that a `watch` script is present in the individual packages.
- `npm run build`: Run the `build` script inside each individual package. This will make sure to run the script in a sequential order to make sure dependencies on each other are followed.
- `npm run version`: Run the `version` command of lerna, with specific arguments to best allow for versioning of packages. This will guide the developer through updating the version of individual packages affected by changes to the files.
