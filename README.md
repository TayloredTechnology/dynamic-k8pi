# Dynamic K8pi (K8's API)

Kubernetes API undergoes regular revisions and structural changes, which unfortunately has resulted in may of the NPM libraries unintentionally locking to specific Kubernetes releases.

This library performs several helper / common functions to assist interaction with Kubernetes API
- Full API & API's Endpoint discovery
- Generation of EndPoint url for any requested 'Kind'
- Always tracks latest API Version for each endpoint i.e. v1 over v1beta1

## ENV's

In-Cluster no environmental variables are necessary as Kubernetes pushes in `namespace` and API Access Keys into all PODS by default. 

When running locally the following ENV's can be relevant
- POD_NAMESPACE: {Default: kube-system}
- CONTEXT: KubeCtl context to use for cluster access

## API

### init(returnStructure = false, globalNamespaces = ['kube-system'])
returnStructure:	Determines if return object should include API structure
globalNamespaces: Array of string values for Namespaces that have been given cluster level access
Returns Promise: 
- (returnStructure = false) [Axios](https://www.npmjs.com/package/axios) object preconfigured with API access
- (returnStructure = true)
	```
	{
		apiStructure, # apiStructure object
		kube					# axios API access object
	}
	```

### formURL(data)
data: expectation is the actual object that should be passed to Kubernetes API but its not necessary to pass the full object.

Returns: 
- URL base string for REST interactions based on the data object passed in.
- null when trying to access a cluster level endpoint from outside a cluster enabled namespace.

```
data object
{
	kind: 'Service'	# API endpoint for access
		metadata: {		# Optional (only used in cluster level namespaces)
			namespace: 'default' 
		}
}
```

## Example

Get a list of all services in `kube-system` namespace
```
const k8pi = require('dynamic-k8pi')
k8pi.init().then(auth => {
	formedURL = k8pi.formURL({kind: 'Service'})
	auth.get(formedURL).then(res => console.log(res.data))
})
```

Delete a specific service `topurge` in `kube-system` namespace
```
const k8pi = require('dynamic-k8pi')
k8pi.init().then(auth => {
	formedURL = k8pi.formURL({kind: 'Service'})
	auth.delete(formedURL + '/topurge').then(res => console.log(res.data))
})
```

Access Cluster level `StorageClass` in custom global namespace `storage`
```
process.env.POD_NAMESPACE = 'storage'
const k8pi = require('dynamic-k8pi')
k8pi.init(false, ['storage']).then(auth => {
	formedURL = k8pi.formURL({kind: 'StorageClass'})
	auth.get(formedURL).then(res => console.log(res.data))
})
```

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for *development and testing* purposes. See deployment for notes on how to deploy the project on a live system.

### Prerequisites

- Node 6.12.x or higher (LTS when development commenced)
- [TAP](https://www.npmjs.com/package/tap)

```
npm install --global tap
```

### Installing

A step by step series of examples that tell you how to get a development env running

```
git clone git@github.com:TayloredTechnology/dynamic-k8pi.git
cd dynamic-k8pi
npm install
```

## Running the tests

[TAP](https://testanything.org/) is used for all tests

```
# Execute all application tests
npm test
```

Code Coverage is provided by [CodeCov](https://codecov.io).

### And coding style tests

[XO](https://github.com/sindresorhus/xo) is used with [Prettier](https://github.com/prettier/prettier) for linting & code style.

```
npm run lint
```

## Built With

- [CodeCov](http://codecov.io/)
- [DependencyCI](http://dependencyci.com/)
- [Node @6.12.x](https://nodejs.org/docs/latest-v6.x/api/)
- [Release IT](https://webpro.github.io/release-it/)
- [RenovateApp](http://renovateapp.com/)
- [SNYK](http://snyk.io/)

## Contributing

Please read [CONTRIBUTING.md]() for details on our code of conduct, and the process for submitting pull requests to us.

## Versioning

We use [SemVer](http://semver.org/) for versioning. For the versions available, see the [tags on this repository](https://github.com/TayloredTechnology/dynamic-k8pi/tags).

## Authors

- **Keidrych Anton-Oates** - *Initial work* - [Taylored Technology](https://tayloredtechnology.net)

See also the list of [contributors](https://github.com/TayloredTechnology/contributors) who participated in this project.

## License

This project is licensed under the Mozilla Public License Version 2.0 - see the [LICENSE](LICENSE) file for details

## Acknowledgments

- NPM Community for consistenly making packages that accelerate development work
- [Test Anything Protocol](https://testanything.org/) for consistenly accelerating Feature Driven Design
