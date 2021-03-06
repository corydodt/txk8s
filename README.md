# txk8s

A Twisted implementation of Kubernetes

## Example program: save and retrieve a secret in the default namespace

```python
import base64

from twisted.internet import defer, task

import txk8s


@defer.inlineCallbacks
def main(reactor):
    txcli = txk8s.TxKubernetesClient()

    # let's create and store a secret
    sec_b64 = base64.b64encode("oh this should definitely not be hardcoded")
    meta = txcli.V1ObjectMeta(name='mysecret')
    body = txcli.V1Secret(data={'myuser': sec_b64}, metadata=meta)

    res = yield txcli.call(txcli.coreV1.create_namespaced_secret, 'default', body)
    print 'Create and store: %r: %r\n' % (res.metadata.self_link, res.data)

    # now let's get the same secret back out
    retrieved_secret = yield txcli.call(txcli.coreV1.read_namespaced_secret, 'mysecret', 'default')
    print 'Retrieve:', retrieved_secret.data

    defer.returnValue(retrieved_secret)


task.react(main)
```

## For maintainers: How to build

1. Increment `__version__` in `__version.py`.
2. Update the Change Log below.
3. Run the following:

```
python setup.py sdist bdist_wheel
twine upload dist/*<version>*
```

## Change Log
### [0.0.1] - 2017-12-21
#### Changed
- v1 yo
