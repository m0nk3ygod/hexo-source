---
title: "🔒 Windows OS & PE 파일 구조"
date: 2025-08-22
categories: ["< Dreamhack >", Windows, "- System Hacking"]
tags: [windows, kernel, usermode, kernelmode]
password: "tjwjddltoddlf"
abstract: "이 글은 보호되어있습니다."
message: "비밀번호를 입력하세요."
---

<br>

# PE (Portable Executable)

**Windows** 운영체제는 다른 운영체제들과 마찬가지로 고유한 시스템 구조와 실행 파일 포맷을 가지고 있다.

Windows에서 사용되는 실행 파일 포맷인 **Portable Executable (PE)** 는  
바이너리 분석 및 익스플로잇에 핵심적인 역할을 한다.

**PE file** 에는 실행 코드뿐만 아니라 함수 호출 정보, 라이브러리(DLL) 의존성, 보안 기능 등이 포함되어 있으며  
이 구조에 대한 이해는 **메모리 구조 분석** 이나 **보호 기법** 우회와 같은 실습에 직접적으로 연결된다.

또한 **Windows** 에서는 시스템 호출 방식, API 호출 구조, 모듈 로딩 방식 등이 독특하게 설계되어 있어  
바이너리의 동작을 정확히 이해하기 위해서는 이러한 운영체제 구조에 대한 기초 지식이 필요하다.

<br>

# Windows NT

**Windows NT (New Technology)** 는 **Microsoft** 에서 개발한 운영체제 중 하나이며  
1993년 7월 27일에 처음 배포되어 **Windows 11** 까지 이어져 오고 있다.

초기 개발 시 [마이크로 커널](https://en.wikipedia.org/wiki/Microkernel) 의 영향을 많이 받았으나  
성능 상의 이유로 순수한 마이크로 커널이 아닌 [하이브리드 커널](https://en.wikipedia.org/wiki/Hybrid_kernel)로 구현되었다.

**Windows** 는 대부분의 운영체제와 동일하게 <U>유저랜드</U>와 <U>커널</U>이 분리되어 있으며  

![Windows OS 구조](/assets/img/dreamhack/2025-08-22-12-56-13.png)

다음과 같은 구조를 가진다.

<br>

# Dynamic Link Library (DLL)

**DLL** 은 동적 링크 파일(Dynamic Link Library)로써  
**Linux** 의 **so** 파일과 동일하게 <U>런타임에 필요한 함수를 불러오는 역할을 수행</U>한다.

> **Linux의 so 파일...?**
>  
> .so (Share Object)는 실행 파일이 **런타임에 동적 링크** 해서 쓰이는 라이브러리이다.  
> 반대 개념으로 .a (정적 라이브러리)가 있는데 둘의 차이는 다음과 같다.
>  
> `.a` (정적 라이브러리) : 링크 시 함수 코드가 실행 파일에 복사됨.  
> \> 실행 파일이 커지고 교체가 어렵다.  
> 
> `.so` (공유 라이브러리) : 실행 시 **로더가 메모리에 매핑** 하여 공유  
> \> 메모리/디스크 절약, 라이브러리 교체 시 용이  

*ntdll.dll* 또한 **DLL** 의 한 종류로 유저랜드와 커널을 이어주는 중간 다리 역할을 수행하는 동적 링크 파일이다.

**Windows** 에서 프로그램을 빌드한 후 **WinDbg** 로 실행해보면 *ntdll.dll* 외에도 다양한 **DLL** 들이 로드되는 모습을 확인할 수 있다.  

그 중 대표적인 **DLL** 들을 추가로 살펴보자.

- VCRUNTIME140.dll  
    - (Visual C++ Runtime v14.0 툴셋)
    - **Microsoft Visual Studio** 로 개발된 프로그램을 실행하기 위해 로드되는 **DLL**

- ucrtbase.dll
    - (Universal C Runtime base)
    - **Microsoft Visual Studio** 로 컴파일된 C, C++ 프로그램에 대한 필수 기능과 라이브러리가 포함된 **DLL**
    - **Linux** 의 libc.so와 유사함

- kernel32.dll
    - (Kernel (Win 32) API)
    - 메모리 관리, 파일 I/O 등, 프로그램을 실행하기 위한 기본적인 기능이 내장되어 있는 **DLL**
    - kernel32.dll의 많은 함수는 내부적으로 ntdll.dll의 함수를 호출하여 실제 작업을 수행하도록 구현된 일종의 Wrapper로 동작함

- KERNELBASE.dll
    - (Kernel Base)
    - kernel32.dll과 거의 동일한 역할을 수행함

- wow64*.dll
    - (Windows-on-Windows 64-bit)
    - `wow64` 로 시작하는 **DLL** 들
    - 32비트 프로그램을 64비트 운영체제에서 돌아갈 수 있도록 도와주는 **DLL**
    - *wow64con.dll* , *wow64.dll* , *wow64win.dll* , *wow64base.dll* 등

<br>

# Portable Executable (PE)

**PE** 는 **Windows** 에서 사용하는 실행 파일 포맷이며  
**Unix** 에서 사용되던 **COFF** 파일 포맷의 확장 버전이다.

흔히 접할 수 있는 *.exe* 나 *.dll* 파일들도 바로 이 PE 구조를 따른다.

> **COFF 파일 포맷??**  
>  
> **COFF(Common Object File Format)** 는 컴파일 후 링커가 사용하는 오브젝트 파일 형식이다.  
> COFF와 그 변종들은 일부 유닉스 계열 시스템, 마이크로솦트 윈도우(PE 포멧), EFI 환경, 그리고 일부 임베디드 개발 시스템에 계속 사용되고 있다.  
>  
> Refer : [COFF - Wiki](https://ko.wikipedia.org/wiki/COFF)

**Linux** 의 **ELF** 포맷과 동일하게 프로그램을 메모리에 올리고 프로그램이 런타임에 정상적으로 동작하게 하기 위한 정보들을 담고 있다.

사용자가 *.exe* 프로그램을 실행하거나 프로세스가 *.dll* 파일을 불러오면  
**Windows** 는 해당 파일의 **PE** 구조를 분석하여 프로그램을 메모리에 적재한다.  

<br>

# PE 파일

PE 구조를 가진 파일을 **PE 파일** 이라고 부르며,  
**PE 파일** 은 <U>프로그램을 메모리에 적재하기 위한 정보</U>와 <U>런타임에 프로그램이 정상적으로 동작하기 위한 정보</U>를 담고 있다.  

<br>

## PE 파일 구조

**PE 파일** 은 크게 **PE 헤더** 와 **PE 바디** 로 구분할 수 있다.

![PE 파일 구조](/assets/img/dreamhack/2025-08-22-16-26-02.png)

<br>

### PE 헤더

**PE 헤더** 는 **Windows** 가 실행 파일( *.exe* )이나 라이브러리( *.dll* )와 같은 프로그램을  
메모리에 적재하고 실행하기 위해 필요한 정보를 담고 있는 부분이다.

![PE 파일 구조 - 헤더](/assets/img/dreamhack/2025-08-22-16-31-28.png)

이미지 좌측에 보이는 구조에서 헤더를 찾을 수 있다.

**DOS 헤더 (`struct _IMAGE_DOS_HEADER`)**

> 해당 프로그램의 버전, 레거시(오래됐지만 여전히 쓰이는 기술/시스템을 의미) 호환성 등을 명시하기 위한 헤더이다.

**Image 헤더 (`struct _IMAGE_NT_HEADER`)**

> 파일 시그니처와 **Section** 의 메타데이터 등을 저장한다.

**Section 헤더 (`struct _IMAGE_SECTION_HEADER`)**

> 실행 코드나 데이터, 리소스 등이 들어있는 **Section** 의 메타데이터를 저장한다.

<br>

실행 시 ****Section**  헤더** 의 정보를 통해 각 섹션의 정보를 메모리에 배치한다.

이때 섹션 헤더란 위 그림의 왼쪽 부분인 `struct _IMAGE_SECTION_HEADER[i]` 들을 의미하며,  
메모리 로드 시 위 그림의 오른쪽과 같은 형태로 올라가게 된다.

**PE 헤더** 는 또 여러 개의 세부 헤더로 구성되어 있다.

<br>

#### DOS 헤더

![PE 파일 구조 - DOS 헤더](/assets/img/dreamhack/2025-08-22-16-57-56.png)

**DOS 헤더** 의 시작은 `struct _IMAGE-DOS_HEADER` 로 시작하며, 아래와 같다.

```c++
typedef struct _IMAGE_DOS_HEADER {  // DOS .EXE header
    uint16_t e_magic;               // Magic number
    uint16_t e_cblp;                // Bytes on last page of file
    uint16_t e_cp;                  // Pages in file
    uint16_t e_crlc;                // Relocations
    uint16_t e_cparhdr;             // Size of header in paragraphs
    uint16_t e_minalloc;            // Minimum extra paragraphs needed
    uint16_t e_maxalloc;            // Maximum extra paragraphs needed
    uint16_t e_ss;                  // Initial (relative) SS value
    uint16_t e_sp;                  // Initial SP value
    uint16_t e_csum;                // Checksum
    uint16_t e_ip;                  // Initial IP value
    uint16_t e_cs;                  // Initial (relative) CS value
    uint16_t e_lfarlc;              // File address of relocation table
    uint16_t e_ovno;                // Overlay number
    uint16_t e_res[4];              // Reserved words
    uint16_t e_oemid;               // OEM identifier (for e_oeminfo)
    uint16_t e_oeminfo;             // OEM information; e_oemid specific
    uint16_t e_res2[10];            // Reserved words
    int32_t e_lfanew;               // File address of new exe header
} IMAGE_DOS_HEADER, *PIMAGE_DOS_HEADER;
```

- **e_magic**  
    - **DOS 헤더** 임을 표현하기 위한 매직 넘버로 "MZ"(`0x5A4D`) 이다.
- **e_lfanew**  
    - **Image 헤더** 의 시작 주소를 가르킨다.

**DOS 헤더** 아래에 DOS Stub이라는 가변의 길이를 가지는 공간이 존재하며,  
주로 "This is program cannot be run in DOS mode" 내용이 문자열로 적혀있다.

> **"This is program cannot be run in DOS mode" 가 뭔데?**
>  
> DOS 시절에는 DOS Stub 영역에 실제 프로그램의 코드가 들어 있었지만  
> Windows 이후에는 이곳이 "이 프로그램은 DOS 모드에서 실행할 수 없습니다." 라는 안내문을 출력하도록 바뀌었다.  
> 이를 통해 구형 DOS 환경에서 프로그램이 실행되어 DOS Stub 영역으로 점프되더라도  
> 파일 형식 오류로 인해 시스템이 멈추는 대신 안내문을 띄울 수 있게 되었다.

<br>

#### Image 헤더

앞에서 살펴본 **DOS 헤더** 의 `e_lfanew` 필드는 **Image 헤더** 의 시작 오프셋을 가리킨다.

![PE 파일 구조 - image 헤더](/assets/img/dreamhack/2025-08-22-16-57-03.png)

**Image 헤더** 는 `struct _IMAGE_NT_HEADERS` 구조체로 정의되어 있다.

```c++
typedef struct _IMAGE_NT_HEADERS {
    uint32_t Signature;
    IMAGE_FILE_HEADER FileHeader;
    IMAGE_OPTIONAL_HEADER32 OptionalHeader;
} IMAGE_NT_HEADERS32, *PIMAGE_NT_HEADERS32;
```

- **Signature**
    - **Image 헤더** 임을 표현하기 위한 매직 넘버로 "PE\0\0"(`50 45 00 00`)이다.

<br>

##### Image 헤더 - 파일 헤더

**Image 헤더** 의 `FileHeader` 필드는 **파일 헤더** 에 해당한다.

![image 헤더 - 파일 헤더](/assets/img/dreamhack/2025-08-22-17-01-15.png)

`struct _IMAGE_FILE_HEADER` 구조체로 정의되어 있다.

```c++
typedef struct _IMAGE_FILE_HEADER {
    uint16_t Machine;
    uint16_t NumberOf**Section** s;
    uint32_t TimeDateStamp;
    uint32_t PointerToSymbolTable;
    uint32_t NumberOfSymbols;
    uint16_t SizeOfOptionalHeader;
    uint16_t Characteristics;
} IMAGE_FILE_HEADER, *PIMAGE_FILE_HEADER;
```

- Machine
    - 해당 프로그램이 어떤 아키텍처에서 돌아갈 수 있는지 나타낸다. ([Machine Code](https://github.com/jasonwhite/ducible/blob/adc6cacdc2405a217f9ea2178b6ec3e2cdc6200c/src/pe/format.h#L118))

- NumberOfSections
    - **PE 파일** 이 가지고 있는 **Section** 개수를 의미한다.
    - 무조건 1개 이상 존재해야 한다.

- TimeDateStamp
    - 해당 프로그램을 빌드한 시각을 나타내며, [Unix 타임스탬프](https://www.unixtimestamp.com/)를 이용한다.

- PointerToSymbolTable, NumberOfSymbols
    - **COFF** 디버그 정보를 가진 경우에만 사용하며, 디버그 심볼 테이블과 심블 개수를 의미한다.
    - 현재는 새로운 **PE** 의 디버그 포맷이 사용됨에 따라 잘 사용되지 않는 값들이다.

- SizeOfOptionalHeader
    - Optional 헤더의 크기를 나타낸다.

- Characteristics
    - **PE 파일** 의 속성 정보를 비트 플래그 형태로 저장하는 공간이다. ([Flags](https://github.com/jasonwhite/ducible/blob/adc6cacdc2405a217f9ea2178b6ec3e2cdc6200c/src/pe/format.h#L92))


<br>

##### Image 헤더 - Optional 헤더

**Image 헤더** 의 `OptionalHeader` 필드는 **Optional 헤더** 에 해당한다.

![image 헤더 - optional 헤더](/assets/img/dreamhack/2025-08-22-17-03-56.png)

해당 정보들은 **Section** 들이 실제로 메모리에 올라갈 때 필요한 정보를 포함하고 있다.

(여기서 말하는 섹션은 리눅스의 `.text`, `.data`, `.bss`와 같은 ELF 파일의 섹션과 유사한 개념이다.)

이는 아래와 같이 `struct _IMAGE_OPTIONAL_HEADER` 구조체로 정의되어 있다.  

```c++
typedef struct _IMAGE_OPTIONAL_HEADER {
    //
    // Standard fields.
    //

    uint16_t Magic;
    uint8_t MajorLinkerVersion;
    uint8_t MinorLinkerVersion;
    uint32_t SizeOfCode;
    uint32_t SizeOfInitializedData;
    uint32_t SizeOfUninitializedData;
    uint32_t AddressOfEntryPoint;
    uint32_t BaseOfCode;
    uint32_t BaseOfData;

    //
    // NT additional fields.
    //

    uint32_t ImageBase;
    uint32_t SectionAlignment;
    uint32_t FileAlignment;
    uint16_t MajorOperatingSystemVersion;
    uint16_t MinorOperatingSystemVersion;
    uint16_t MajorImageVersion;
    uint16_t MinorImageVersion;
    uint16_t MajorSubsystemVersion;
    uint16_t MinorSubsystemVersion;
    uint32_t Win32VersionValue;
    uint32_t SizeOfImage;
    uint32_t SizeOfHeaders;
    uint32_t CheckSum;
    uint16_t Subsystem;
    uint16_t DllCharacteristics;
    uint32_t SizeOfStackReserve;
    uint32_t SizeOfStackCommit;
    uint32_t SizeOfHeapReserve;
    uint32_t SizeOfHeapCommit;
    uint32_t LoaderFlags;
    uint32_t NumberOfRvaAndSizes;
    IMAGE_DATA_DIRECTORY DataDirectory[IMAGE_NUMBEROF_DIRECTORY_ENTRIES];
} IMAGE_OPTIONAL_HEADER32, *PIMAGE_OPTIONAL_HEADER32;
```

- Magic
    - 32비트의 경우 `0x010b`, 64비트의 경우 `0x020b` 를 가지는 매직 넘버이다.

- MajorLinkerVersion, MinorLinkerVersion
    - 해당 실행 파일을 만든 **링커(Linker)** 의 버전을 의미한다.

- SizeOfCode
    - `.text` 섹션의 크기를 나타낸다.

- SizeOfInitializedData, SizeOfUninitializedData
    - **SizeOfInitializedData** 는 **초기화된 데이터 섹션** (예: `.data`, `.rdata`)의 크기를 나타낸다.
    - **SizeOfUninitializedData** 는 **초기화되지 않은 데이터 섹션** (예: .bss)의 크기를 나타낸다.

- AddressOfEntryPoint
    - 프로그램이 실행되면 가장 먼저 실행될 코드의 주소인 진입점을 나타낸다.

- BaseOfCode
    - 첫 번째 **코드 섹션** (예: `.text`)이 시작되는 주소를 나타낸다.

- BaseOfData
    - 첫 번째 **데이터 섹션** 이 시작되는 주소를 나타낸다.

- ImageBase
    - **PE 파일** 이 메모리에 로드될 때의 시작 주소를 나타낸다.
    - **일반적으로 EXE 파일** 의 경우 `0x400000`, **DLL 파일** 의 경우 `0x10000000` 이다.

- SectionAlignment
    - 메모리에서 섹션 간의 정렬(Alignment) 크기를 의미한다.
    - 예를 들어, **SectionAlignment** 가 `0x1000` 이라면 메모리 상에 올라간 각 섹션의 크기는 `0x1000` 의 배수여야 한다.

- FileAlignment
    - 파일에서 섹션 간의 정렬(Alignment) 크기를 의미한다.
    - 예를 들어, **FileAlignment** 가 `0x1000` 이라면 각 **Section** 의 크기는 `0x1000` 의 배수여야 한다.

- SizeOfImage
    - **PE 파일** 이 메모리에 적재되었을 때의 크기를 나타낸다.

- SizeOfHeaders
    - **Header** 의 전체 크기를 의미한다.

- DataDirectory[IMAGE_NUMBEROF_DIRECTORY_ENTRIES]
    - `IMAGE_NUMBEROF_DIRECTORY_ENTRIES` 는 PE 파일에서 사용되는 데이터 디렉터리 항목의 최대 개수를 나타내는 상수이며, Windows에서는 이 상수가 [16](https://github.com/jasonwhite/ducible/blob/adc6cacdc2405a217f9ea2178b6ec3e2cdc6200c/src/pe/format.h#L158C1-L158C44)으로 정의되어 있다.
    - 본 **DataDirectory** 배열의 각 요소는 다음과 같이 `struct IMAGE_DATA_DIRECTORY` 구조체로 정의되어 있으며, 각각 가상 주소(`VirtualAddress`)와 크기(`Size`)를 포함한다.
    - 배열 내부의 각 인덱스의 역할은 구조체로 정의되어 있으며 각 `DataDirectory` 배열은 인덱스에 따라 서로 다른 용도로 사용된다. 예를 들어, 0번째 인덱스인 `DataDirectory[0]` 은 Export Table로, 1번째인 `DataDirectory[1]` 은 Import Table로 사용된다. 이처럼 각 인덱스마다 용도가 정해져 있으며, 보다 다양한 용도는 [define](https://github.com/jasonwhite/ducible/blob/adc6cacdc2405a217f9ea2178b6ec3e2cdc6200c/src/pe/format.h#L279)에 설명되어 있다.

<br>

#### **Section**  헤더

![PE 헤더 - Section 헤더](/assets/img/dreamhack/2025-08-22-17-30-52.png)

**Section 헤더** 는 아래와 같이 `struct _IMAGE_SECTION_HEADER` 구조체로 정의되어 있으며,  
배열처럼 여러개의 **Section 헤더** 가 연속해서 존재할 수 있다.

```c++
#define IMAGE_SIZEOF_SHORT_NAME 8

typedef struct _IMAGE_SECTION_HEADER {
    uint8_t Name[IMAGE_SIZEOF_SHORT_NAME];
    union {
        uint32_t PhysicalAddress;
        uint32_t VirtualSize;
    } Misc;
    uint32_t VirtualAddress;
    uint32_t SizeOfRawData;
    uint32_t PointerToRawData;
    uint32_t PointerToRelocations;
    uint32_t PointerToLinenumbers;
    uint16_t NumberOfRelocations;
    uint16_t NumberOfLinenumbers;
    uint32_t Characteristics;
} IMAGE_SECTION_HEADER, *PIMAGE_SECTION_HEADER;
```

리눅스의 `.text`, `.data`와 같은 섹션의 정보를 담고 있는 헤더이다.

- Name
    - 섹션의 이름을 문자열 형태로 저장한다.
    - 그 중 가장 많이 사용되는 **Section** 들은 아래와 같다.
        - `.text`
            - 실행 가능한 코드들을 포함하는 **Section** 이다.
            - `r-x` 권한으로 메모리에 적재된다.
        - `.data`
            - 초기화된 전역 변수들을 포함하는 **Section** 이다.
            - `rw-` 권한으로 메모리에 적재된다.
        - `.rdata`
            - 상수와 같이 불변의 값들이 포함되는 **Section** 이다.
            - `r--` 권한으로 메모리에 적재된다.
        - `.bss`
            - 초기화되지 않은 전역 변수들을 포함하는 **Section** 이다.
            - `rw-` 권한으로 메모리에 적재된다.
        - `.idata`
            - **Import Address Table (IAT)** 를 포함하는 **Section** 이다.
            - `r--` 권한으로 메모리에 적재된다.
        - `.edata`
            - **Export Address Table (EAT)** 를 포함하는 **Section** 이다.
            - `r--` 권한으로 메모리에 적재된다.
        - `.rsrc`
            - 아이콘과 같이 실행에 필요한 다양한 자원을 포함하며, **GUI** 프로그램에서 자주 보인다.
            - `r--` 권한으로 메모리에 적재된다.
        - `.reloc`
            - PE 파일이 메모리에 적재될 때 `ImageBase` 가 아닌 다른 주소에 할당될 경우 재배치하기 위한 정보를 담고 있는 섹션이다.
            - **Image 헤더** 에 속한 **Optional 헤더** 에는 PE 파일이 기본으로 로드될 주소인 `ImageBase` 가 정의되어 있다. 만약 프로그램이 **ASLR** 과 같은 이유로 인해 `ImageBase` 가 아닌 다른 주소에 로드되는 경우 프로그램 내부의 포인터, 함수 주소, 전역 변수 등의 주소가 틀어지게 되며, 이때 프로그램을 올바르게 재배치하기 위해 사용한다.
            - `r--` 권한으로 메모리에 적재된다.
        - `.tls`
            - **Static Thread Local Storage**를 지원하기 위한 섹션이다.

- Misc
    - 실제로는 `VirtualSize` 만 사용되며 해당 섹션이 메모리에 올라갔을 때 차지하는 영역의 크기를 나타낸다.

- VirtualAddress
    - 해당 **Section** 이 메모리에 올라갈 때 `ImageBase + VirtualAddress` 에 로드된다.

- SizeOfRawData
    - 파일에서 해당 **Section** 이 차지하는 크기를 나타낸다.

- PointerToRawData
    - 파일에서 해당 **Section** 의 위치를 가리킨다.

- PointerToRelocations, NumberOfRelocations
    - *.obj* 파일에서만 사용하는 필드이다. *.obj* 파일은 컴파일된 코드의 중간 산출물로 *.exe* 나 *.dll* 를 만들기 위해 링커의 입력으로 사용되는 파일이다.
    - **NumberOfRelocations** 는 **PointerToRelocations** 가 가리키는 구조체 배열의 개수를 나타낸다.
    - **PointerToRelocations** 는 IMAGE_RELOCATION 구조체 배열의 주소 혹은 `nullptr` 값을 가진다.

- PointerToLinenumbers, NumberOfLinenumbers
    - **COFF** 스타일의 라인 번호가 **PE 파일**에 사용되는 경우 사용된다.
    - **NumberOfLinenumbers** 는 **PointerToLinenumbers** 가 가리키는 구조체 배열의 개수를 나타낸다.
    - **PointerToLinenumbers** 는 **IMAGE_LINENUMBER** 구조체 배열의 주소 혹은 `nullptr` 값을 가진다.

- Characteristics
    - 해당 섹션의 속성을 나타내며 [Characteristics 비트 플래그](https://learn.microsoft.com/ko-kr/windows/win32/api/winnt/ns-winnt-image_section_header) 형태로 정보를 저장한다.

<br>

# 정리

Windows 운영체제의 아키텍처는 Windows NT 기반으로 하이브리드 커널을 채택하고 있다.

이는 사용자 모드와 커널 모드 간의 효율적인 통신을 가능하게 한다.

주요 API는 ntdll.dll을 통해 제공되며, 이를 통해 유저 랜드와 커널 간의 상호작용이 이루어진다.

동적 연결 라이브러리(DLL)은 실행 시 필요한 함수를 로드하기 위한 파일로,  
Windows에서는 이를 이용하여 모듈화하고 런타임에 필요한 함수를 로드한다.
(이는 linux의 so 파일과 유사한 역할을 한다.)

Portable Executable (PE) 는 Windows에서 사용하는 실행 파일 포맷으로, 코드, 데이터, 리소스 등의 정보를 포함하여, 실행 파일의 구조를 정의한다.

PE 파일은 실행 파일의 시작을 나타내는 헤더와 해당 파일의 섹션들을 포함한다.

PE 파일은 Windows에서 프로그램을 실행하는 데 필요한 정보를 제공한다.

<br>

Windows OS & PE 끝

<br>

## Refer
- https://www.researchgate.net/figure/Portable-executable-file-format_fig6_338355873
- https://tech-zealots.com/malware-analysis/pe-portable-executable-structure-malware-analysis-part-2/
- https://ko.wikipedia.org/wiki/COFF
- https://github.com/jasonwhite/ducible/blob/adc6cacdc2405a217f9ea2178b6ec3e2cdc6200c/src/pe/format.h#L118
- https://www.unixtimestamp.com/
- https://github.com/jasonwhite/ducible/blob/adc6cacdc2405a217f9ea2178b6ec3e2cdc6200c/src/pe/format.h#L92
- https://github.com/jasonwhite/ducible/blob/adc6cacdc2405a217f9ea2178b6ec3e2cdc6200c/src/pe/format.h#L158C1-L158C44
- https://github.com/jasonwhite/ducible/blob/adc6cacdc2405a217f9ea2178b6ec3e2cdc6200c/src/pe/format.h#L279
- https://learn.microsoft.com/ko-kr/windows/win32/api/winnt/ns-winnt-image_section_header
- https://dreamhack.io/ (Windows SystemHacking Path)