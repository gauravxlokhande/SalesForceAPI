## Documentation:
```
http://www.dneonline.com/calculator.asmx
```

 Step 1: <p>From wsdl file only one binding is allowd by salesforce so if more than one bindings occurs then remove all extra bindings from WSDL file.</p>

## WSDL File
```
http://www.dneonline.com/calculator.asmx?WSDL
```

## Remotesite Setting
```
 http://www.dneonline.com
```

Step 2: <p>After Removing all bindings go into Org--> "Apex Classes"</p>
Step 3: <P> Click On "Generate From WSDL" and upload WSDl File in it. and Click "Generate Apex Code"  </P>
Step 4: <P>After That u get two Apex Classes 1."tempuriOrg" & 2."AsyncTempuriOrg" </P>
Step 5: <p>Access this classes in developer console</p>

## By creating object peform operations through soap callouts on data through SOAP Callout.

```
CalculatorService.CalculatorSoap calculator = new  CalculatorService.CalculatorSoap();
Integer x = 6;
Integer y = 3;
Integer result = calculator.Add(x,y);
System.debug(x + ' + ' + y + ' = ' + result);
Integer result1 = calculator.Subtract(x,y);
System.debug(x + ' - ' + y + ' = ' + result1);
Integer result2 = calculator.Multiply(x,y);
System.debug(x + ' * ' + y + ' = ' + result2);
Integer result3 = calculator.Divide(x,y);
System.debug(x + ' / ' + y + ' = ' + result3);
```


