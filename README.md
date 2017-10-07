until nanoserver supports the c++ build tools this is probably
worse than building stuff on a windows virtual machine as you
will be using a 4GB windows server docker image.

includes:
* Visual C++ Build Tools 2017
* 7zip
* git
* chocolatey

usage:
put your build commands in a file named ```release.ps1``` and mount
the directory containing that script as C:\build

example:
```ps1
cd C:\my\project
# put your stuff in release.ps1
docker pull lolisamurai/msvc
docker run --rm -v (Get-Location).Path:c:\build lolisamurai/msvc
```

to use cl and other msvc tools from your powershell script you need
to source ```C:\Program Files (x86)\Microsoft Visual Studio\2017\BuildTools\Common7\Tools\VsDevCmd.bat```,
which can be done with a script such as:

```ps1
# https://stackoverflow.com/questions/2124753/how-can-i-use-powershell-with-the-visual-studio-command-prompt

param (
    [string]$arch = "amd64"
)

Push-Location "C:\Program Files (x86)\Microsoft Visual Studio\2017\BuildTools\Common7\Tools"
cmd /c "VsDevCmd.bat -arch=$arch&set" |
ForEach-Object {
    if ($_ -match "=") {
        $v = $_.split("="); set-item -force -path "ENV:\$($v[0])"  -value "$($v[1])"
    }
}
Pop-Location
Write-Host "`nVisual Studio 2017 Command Prompt variables set." -ForegroundColor Yellow
```

save it as vcvarsall17.ps1 and invoke it like so:

```
.\vcvarsall.ps1
```

you can specify the target architecture if desired.

for an example project that uses this container, see
[tinynx](https://github.com/Francesco149/tinynx).
