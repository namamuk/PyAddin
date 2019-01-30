# PyAddin

PyAddin is an Excel addin template with customized Ribbon menu and a combination of VBA and Python. VBA calls Python script by console arguments, and gets return from running results. So it's easily extended to your own application.

**Shown with VBA and Driven by Python**

---

## Quick Start

1. Customize the Ribbon Tab by editing the embedded `pyAddin.xlam\customUI\CustomUI.xml`

```xml
<customUI xmlns="http://schemas.microsoft.com/office/2006/01/customui">
  <ribbon startFromScratch="false">
    <tabs>
      <tab id="userRibbon" label="PyAddin">
        <group id="g1" label="My Test">
            <button id="btn_cal" 
              imageMso="Calculator" 
              size="large" 
              label="A1/A2" 
              onAction="callback_cal"/>
        </group>
          
        <group id="g2" label="About">
            <button id="btn_about" 
              imageMso="About" 
              size="large" 
              label="About" 
              onAction="callback_about"/>
        </group>
      </tab>
    </tabs>
  </ribbon>
</customUI>
```

2. Add user defined callback in VBA project

```vba
Sub callback_cal(control As IRibbonControl)

    '''
    ' get value in cell A1, A2, then calculate A1/A2 in python
    '
    '''
    
    Dim a1$, a2$, args, res$
    
    a1 = ActiveSheet.Range("A1").Value
    a2 = ActiveSheet.Range("A2").Value
    
    args = Array(a1, a2)
    If RunPython("scripts.test.division", args, res) Then
        ActiveSheet.Range("A3").Value = res
    Else
        MsgBox res
    End If
    
End Sub
```

`RunPython` is a pre-defined VBA function to call Python scripts from command line, and check return from output/error file generated by the called Python script. The first argument, "scripts.test.division" in this case, refers to the called method.

3. Create Python script under `scripts` to do the main work

```python
# scripts/test.py

def division(a, b):
	assert a!='', 'cell A1 is empty'
	assert b!='', 'cell A2 is empty'
	return float(a)/float(b)
```