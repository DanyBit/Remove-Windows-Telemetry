@echo off
echo ================================================
echo     Script per Disattivare la Telemetria
echo ================================================
echo Operazione in corso... Verifica dei permessi.

:: Controllo se lo script è eseguito con privilegi elevati
net session >nul 2>&1
if %errorLevel% neq 0 (
    echo.
    echo   Questo script deve essere eseguito come amministratore.
    echo Chiudi e clicca con il tasto destro su "Esegui come amministratore".
    pause
    exit /b
)

echo.
echo  Permessi amministratore confermati.
echo Disattivo la telemetria di Windows...

:: Modifica del Registro per bloccare la telemetria
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 0 /f >nul

:: Arresto e disabilitazione dei servizi di raccolta dati
sc stop DiagTrack >nul
sc config DiagTrack start= disabled >nul

sc stop dmwappushservice >nul
sc config dmwappushservice start= disabled >nul

:: Disabilitazione dei task schedulati legati alla telemetria
schtasks /Change /TN "Microsoft\Windows\Application Experience\ProgramDataUpdater" /Disable >nul
schtasks /Change /TN "Microsoft\Windows\Autochk\Proxy" /Disable >nul
schtasks /Change /TN "Microsoft\Windows\Customer Experience Improvement Program\Consolidator" /Disable >nul
schtasks /Change /TN "Microsoft\Windows\Customer Experience Improvement Program\KernelCeipTask" /Disable >nul
schtasks /Change /TN "Microsoft\Windows\Customer Experience Improvement Program\UsbCeip" /Disable >nul
schtasks /Change /TN "Microsoft\Windows\DiskDiagnostic\Microsoft-Windows-DiskDiagnosticDataCollector" /Disable >nul

echo.
echo  Tutto fatto. La telemetria è ora disattivata.
echo  Riavvia il sistema per rendere effettive tutte le modifiche.
echo.
echo — Script creato con cura da danybit
pause
