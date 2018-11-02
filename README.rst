asyncqt - asyncio + Qt
======================

.. image:: https://travis-ci.org/gmarull/asyncqt.svg?branch=master
    :target: https://travis-ci.org/gmarull/asyncqt
    :alt: Build Status

.. image:: https://ci.appveyor.com/api/projects/status/xkdglu72eosg0cbd?svg=true
    :target: https://ci.appveyor.com/project/gmarull/asyncqt
    :alt: Build Status

.. image:: https://codecov.io/gh/gmarull/asyncqt/branch/master/graph/badge.svg
    :target: https://codecov.io/gh/gmarull/asyncqt
    :alt: Coverage

.. image:: https://img.shields.io/pypi/v/asyncqt.svg
    :target: https://pypi.python.org/pypi/asyncqt
    :alt: PyPI Version

``asyncqt`` is an implementation of the ``PEP 3156`` event-loop with Qt. This
package is a fork of ``quamash``.

Requirements
============

``asyncqt`` requires Python >= 3.5 and PyQt5 or PySide2. The Qt API can be
explicitely set by using the ``QT_API`` environment variable.

Installation
============

``pip install asyncqt``

Usage
=====

.. code:: python

    import sys
    import asyncio
    import time

    from PyQt5.QtWidgets import QApplication, QProgressBar
    from asyncqt import QEventLoop, QThreadExecutor

    app = QApplication(sys.argv)
    loop = QEventLoop(app)
    asyncio.set_event_loop(loop)

    progress = QProgressBar()
    progress.setRange(0, 99)
    progress.show()

    async def master():
        await first_50()
        with QThreadExecutor(1) as exec:
            await loop.run_in_executor(exec, last_50)

    async def first_50():
        for i in range(50):
            progress.setValue(i)
            await asyncio.sleep(.1)

    def last_50():
        for i in range(50,100):
            loop.call_soon_threadsafe(progress.setValue, i)
            time.sleep(.1)

    with loop:
        loop.run_until_complete(master())
