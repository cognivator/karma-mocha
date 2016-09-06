# karma-mocha-runner

[![Build Status](https://img.shields.io/travis/cognivator/karma-mocha-runner/master.svg?style=flat-square)](https://travis-ci.org/cognivator/karma-mocha-runner) [![js-standard-style](https://img.shields.io/badge/code%20style-standard-brightgreen.svg?style=flat-square)](https://github.com/cognivator/karma-mocha-runner) 

<!-- [![npm version](https://img.shields.io/npm/v/karma-mocha.svg?style=flat-square)](https://www.npmjs.com/package/karma-mocha)-->
<!--[![npm downloads](https://img.shields.io/npm/dm/karma-mocha.svg?style=flat-square)](https://www.npmjs.com/package/karma-mocha)-->


<!--[![Dependency Status](https://img.shields.io/david/karma-runner/karma-mocha.svg?style=flat-square)](https://david-dm.org/karma-runner/karma-mocha) [![devDependency Status](https://img.shields.io/david/dev/karma-runner/karma-mocha.svg?style=flat-square)](https://david-dm.org/karma-runner/karma-mocha#info=devDependencies)-->

> Adapter for the [Mocha](http://mochajs.org/) testing framework.
> Forked from  [karma-mocha](https://github.com/karma-runner/karma-mocha) in order to add access to the mocha runner instance and its events.

## Primary Documentation

Please refer to the [karma-mocha](https://github.com/karma-runner/karma-mocha) site for documentation.

## Access to the Mocha test runner instance

The primary reason for this fork is to provide access to the Mocha test runner instance. With access to the runner, you gain the ability to listen to its [events](https://github.com/mochajs/mocha/blob/8cae7a34f0b6eafeb16567beb8852b827cc5956b/lib/runner.js#L47-L57).

One possible use for these events is to help mitigate memory usage for large test suites. By handling the 'suite end' and/or 'test end' events, some of the mocha test suite context can be explicitly released in ways mocha itself does not.

> _Inspired by this [mocha issue comment](https://github.com/mochajs/mocha/issues/1991#issuecomment-168248887)_

```js
var runner = getRunner(); // obtain the runner instance by some means... (TBD @@@)

runner.on('suite end', function(suite) {
  // ASSUMPTION: 'suite end' event takes into account any async `before...` and `after...` hooks, i.e. those
  //  that use the `done()` callback.
  delete suite._afterAll;
  delete suite._afterEach;
  delete suite._beforeAll;
  delete suite._beforeEach;
  delete suite.ctx;
  delete suite.suites;
  delete suite.tests;
});

runner.on('test end', function(test) {
  // DO NOT alter tests that should be retained due to use of the `done()` callback.
  if (! test.async) {
    delete test.ctx;
  }
});
```



