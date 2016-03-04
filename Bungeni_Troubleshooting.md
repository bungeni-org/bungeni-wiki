

# Introduction

This page documents typical problems in Bungeni - and suggests resolutions to these issues.

## Bungeni Starts up but unable to browse to the service

Bungeni can be browsed on port 8081 but the portal interface (8080) cannot be accessed even though the portal service is running --

**check the host and port entries in the deploy.ini file for the portal - If they are bound to a specific IP then you must browse Bungeni using that IP.**


## Unit Test failure for openoffice

Usually either of these errors :

```
Failure in test
/home/undesa/bungeni_apps/bungeni/src/bungeni.main/bungeni/ui/downloaddocument.txt
Failed doctest test for downloaddocument.txt
 File "/home/undesa/bungeni_apps/bungeni/src/bungeni.main/bungeni/ui/downloaddocument.txt",
line 0

----------------------------------------------------------------------
File "/home/undesa/bungeni_apps/bungeni/src/bungeni.main/bungeni/ui/downloaddocument.txt",
line 71, in downloaddocument.txt
Failed example:
   output = reportPDF()
Exception raised:
   Traceback (most recent call last):
     File "/home/undesa/bungeni_apps/bungeni/eggs/zope.testing-3.7.1-py2.6.egg/zope/testing/doctest.py",
line 1356, in __run
       compileflags, 1) in test.globs
     File "<doctest downloaddocument.txt[35]>", line 1, in <module>
       output = reportPDF()
     File "/home/undesa/bungeni_apps/bungeni/src/bungeni.main/bungeni/ui/downloaddocument.py",
line 170, in __call__
       return self.documentData(cached=True)
     File "/home/undesa/bungeni_apps/bungeni/src/bungeni.main/bungeni/ui/downloaddocument.py",
line 135, in documentData
       attached_file.file_data = self.generateDoc()
     File "/home/undesa/bungeni_apps/bungeni/src/bungeni.main/bungeni/ui/downloaddocument.py",
line 103, in generateDoc
       return self.error_template()
     File "/home/undesa/bungeni_apps/bungeni/eggs/zope.app.pagetemplate-3.6.0-py2.6.egg/zope/app/pagetemplate/viewpagetemplatefile.py",
line 83, in __call__
       return self.im_func(im_self, *args, **kw)
     File "/home/undesa/bungeni_apps/bungeni/eggs/zope.app.pagetemplate-3.6.0-py2.6.egg/zope/app/pagetemplate/viewpagetemplatefile.py",
line 51, in __call__
       sourceAnnotations=getattr(debug_flags, 'sourceAnnotations', 0),
     File "/home/undesa/bungeni_apps/bungeni/eggs/zope.pagetemplate-3.5.0-py2.6.egg/zope/pagetemplate/pagetemplate.py",
line 115, in pt_render
       strictinsert=0, sourceAnnotations=sourceAnnotations)()
     File "/home/undesa/bungeni_apps/bungeni/eggs/zope.tal-3.5.1-py2.6.egg/zope/tal/talinterpreter.py",
line 271, in __call__
       self.interpret(self.program)
     File "/home/undesa/bungeni_apps/bungeni/eggs/zope.tal-3.5.1-py2.6.egg/zope/tal/talinterpreter.py",
line 343, in interpret
       handlers[opcode](self, args)
     File "/home/undesa/bungeni_apps/bungeni/eggs/zope.tal-3.5.1-py2.6.egg/zope/tal/talinterpreter.py",
line 867, in do_useMacro
       macro = self.engine.evaluateMacro(macroExpr)
     File "/home/undesa/bungeni_apps/bungeni/eggs/zope.tales-3.4.0-py2.6.egg/zope/tales/tales.py",
line 696, in evaluate
       return expression(self)
     File "/home/undesa/bungeni_apps/bungeni/eggs/zope.tales-3.4.0-py2.6.egg/zope/tales/expressions.py",
line 217, in __call__
       return self._eval(econtext)
     File "/home/undesa/bungeni_apps/bungeni/eggs/zope.tales-3.4.0-py2.6.egg/zope/tales/expressions.py",
line 194, in _eval
       ob = self._subexprs[-1](econtext)
     File "/home/undesa/bungeni_apps/bungeni/eggs/zope.tales-3.4.0-py2.6.egg/zope/tales/expressions.py",
line 124, in _eval
       ob = self._traverser(ob, element, econtext)
     File "/home/undesa/bungeni_apps/bungeni/eggs/zope.app.pagetemplate-3.6.0-py2.6.egg/zope/app/pagetemplate/engine.py",
line 68, in __call__
       request=request)
     File "/home/undesa/bungeni_apps/bungeni/eggs/zope.traversing-3.7.2-py2.6.egg/zope/traversing/adapters.py",
line 139, in traversePathElement
       return traversable.traverse(nm, further_path)
     File "/home/undesa/bungeni_apps/bungeni/eggs/zope.traversing-3.7.2-py2.6.egg/zope/traversing/adapters.py",
line 50, in traverse
       return subject[name]
     File "/home/undesa/bungeni_apps/bungeni/eggs/zope.app.basicskin-3.4.0-py2.6.egg/zope/app/basicskin/standardmacros.py",
line 41, in __getitem__
       page = getMultiAdapter((context, request), name=name)
     File "/home/undesa/bungeni_apps/bungeni/eggs/zope.component-3.6.0-py2.6.egg/zope/component/_api.py",
line 111, in getMultiAdapter
       raise ComponentLookupError(objects, interface, name)
   ComponentLookupError: ((<bungeni.models.domain.Report object at
0x55f5850>, <zope.publisher.browser.TestRequest instance
URL=http://127.0.0.1>), <InterfaceClass zope.interface.Interface>,
'ploned-layout')

```

and :

```
ERROR:bungeni.ui:An error occured during ODT/PDF generation
Traceback (most recent call last):
  File "/home/undesa/bungeni2.6/bungeni_apps/bungeni/src/bungeni.main/bungeni/ui/downloaddocument.py", line 100, in generateDoc
    renderer.run()
  File "/home/undesa/bungeni2.6/bungeni_apps/bungeni/eggs/appy-0.5.4-py2.6.egg/appy/pod/renderer.py", line 311, in run
    self.finalize()
  File "/home/undesa/bungeni2.6/bungeni_apps/bungeni/eggs/appy-0.5.4-py2.6.egg/appy/pod/renderer.py", line 416, in finalize
    self.callOpenOffice(resultOdtName, resultType)
  File "/home/undesa/bungeni2.6/bungeni_apps/bungeni/eggs/appy-0.5.4-py2.6.egg/appy/pod/renderer.py", line 384, in callOpenOffice
    raise pe
PodError: An error occurred during the conversion. Could not connect to OpenOffice on port 2002. UNO (OpenOffice API) says: Connector : couldn't connect to socket (Success).

```


Indicates openoffice is not running.

## PDF conversion does not work in Bungeni

PDF conversion does not work in Bungeni
  * Check if the OpenOffice service is started in the background :
```
 $ps aux | grep soffice
 /usr/bin/soffice -accept=socket,port=2002;urp; -nologo -nofirststartwizard -headless
```

  * Check if supporting software for pdf conversion is installed - e.g libtidy :
```
sudo apt-get install libtidy-dev
```

  * PDF conversion will not work out of the box with custom installed openoffice installations -e.g. if you install the version of OOo from the openoffice.org website. To fix this -- edit `openoffice.zcml` in the buildout root folder  -- and edit the openofficepath to the appropriate location of the openoffice-uno-python on your system :

```
<configure xmlns="http://namespaces.zope.org/zope"
           xmlns:meta="http://namespaces.zope.org/meta"
           xmlns:files="http://sample.namespaces.zope.org/files"
           >
    <include package="bungeni.ui" file="meta.zcml" />
    
    <!-- Set the location of a UNO enabled python on the line below -->
    <files:openofficepath path="/opt/openoffice.org3/program/python"/>

</configure>

```

## Locale Error on startup

If you get a error like this on startup :

```
 File "/home/undesa/disk1/bungeni/cap_installs/python25/lib/python2.5/locale.py",
line 514, in getpreferredencoding
   setlocale(LC_CTYPE, "")
 File "/home/undesa/disk1/bungeni/cap_installs/python25/lib/python2.5/locale.py",
line 478, in setlocale
   return _setlocale(category, locale)
Error: unsupported locale setting
```

You need to configure locales on your operating system. On Desktop installations of linux this comes configured out of the box ; this error is usually encountered on headless linux installations.

To resolve this problem, setup and configure a default locale :

```
sudo apt-get install language-pack-en
sudo dpkg-reconfigure locales
```