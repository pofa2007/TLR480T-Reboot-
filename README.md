; #INDEX# =======================================================================================================================
; Title .........: TP-Link TL-R480T+ reboot
; AutoIt Version : 3.2.10++
; Language ......: English
; Description ...: Script to reboot a TP-Link TL-R480T+ firewall
; Author(s) .....: Paulo Ferreira (pofa2007 at gmail dot com)
;                  
; 2015-07-21 PLEASE KEEP THIS HEADER
; ===============================================================================================================================


$ip="192.168.10.1"
$port="23" ;telnet

OnAutoItExitRegister("OnAutoItExit")

TCPStartup()
For $0 = 0 To 10
    $Socket = TCPConnect($ip, $port)
	ConsoleWrite("TCPConnect @error=" & @error &  @CRLF)
    ;Connects to an open socket on the server...
    If $Socket <> -1 Then ExitLoop
    TCPCloseSocket($Socket)
    Sleep(300)
 Next
ConsoleWrite("socket=" & $Socket &  @CRLF)
If $Socket = -1 Then Exit
   
$rv=WaitSend($socket,"Username","xxxx" & @crlf)
$rv=WaitSend($socket,"Password:","xxxx" & @crlf)
$rv=WaitSend($socket,"TP-LINK","enable" & @crlf)
$rv=WaitSend($socket,"Enter password","admin" & @crlf)
$rv=WaitSend($socket,"TP-LINK","sys reboot" & @crlf)
$rv=WaitSend($socket,"will reboot system","Y" & @crlf)
TCPCloseSocket($Socket)
exit

;------------------------------------------------------
Func WaitSend($socket,$wait,$send)
   while 1
	  $sReceived = WaitForInput($socket)
	  if StringInStr($sReceived,$wait) <> 0  then 
		 TCPSend($socket,$send)
		 ExitLoop
	  endif
   wend
endfunc
;------------------------------------------------------
Func WaitForInput($socket)
   While 1
	  sleep(100)
	  $data = TCPRecv($socket, 512)
	  If $data <> "" Then
		 ;ConsoleWrite("TCPRecv=[" & $data & "]" & @CRLF)
		 ConsoleWrite($data)
		 Return $data
	  endif
   Wend
EndFunc

;------------------------------------------------------
Func OnAutoItExit()
    TCPShutdown() ; Close the TCP service.
EndFunc   ;==>OnAutoItExit
