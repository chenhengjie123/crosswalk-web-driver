This is docker for xwalkdriver([crosswalk-web-driver](https://github.com/crosswalk-project/crosswalk-web-driver)).  **ONLY support real device for now.**


# Usage:

1. Add your real device to boot2docker-vm in virtualbox. Otherwise docker cannot get connection with real devices 

2. Run commands below:

```
# Launch xwalkdriver as remote server
$ docker run -d -p 9515:9515 -v /dev/bus/usb:/dev/bus/usb --privileged chenhengjie123/crosswalk-web-driver
da0014b462a0ecd4b2b7f5f38e7f84d2dc97f2fdb0789ab35d90b876daaa74be

# Optional: get xwalkdriver logs in real time
$ docker logs -f da0014b462a0ecd4b2b7f5f38e7f84d2dc97f2fdb0789ab35d90b876daaa74be
```

3. Run your test script. The script may look like below(python):

```
 -*- coding:utf-8 -*-
import os
import unittest

from selenium import webdriver
from time import sleep

# Returns abs path relative to this file and not cwd
PATH = lambda p: os.path.abspath(
    os.path.join(os.path.dirname(__file__), p)
)

class TestCrossWalk(unittest.TestCase):
    def setUp(self):
        capabilities = {
          'xwalkOptions': {
            'androidPackage': 'com.HCCB.score',
            'androidActivity': '.MainActivity',
            'adb-port': 5037,
	  }
	}
        self.driver = webdriver.Remote('http://192.168.59.103:9515', capabilities)

    def tearDown(self):
        self.driver.quit()

    def test_login(self):
        print self.driver.page_source
        self.driver.find_element_by_id("offlineBtn").click()
        print self.driver.page_source


if __name__ == '__main__':
    suite = unittest.TestLoader().loadTestsFromTestCase(TestCrossWalk)
    unittest.TextTestRunner(verbosity=2).run(suite)
```

The ip address `192.168.59.103` must be the ip address of your boot2docker/host. If you're using boot2docker, its ip address can be found by command like below:

```
$ boot2docker ip
192.168.59.103
```
