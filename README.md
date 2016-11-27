# MacOSX-SparseFile-KernelMode

##License

The license model is a BSD Open Source License. This is a non-viral license, only asking that if you use it, you acknowledge the authors, in this case Slava Imameev.

##Features

This is a sparse file implementation that can be used from macOS IOKit modules/kernel extensions. The DldSparseFile class is a C++ IOKit class that can be instantiated from IOKit module. The class is inherited from OSObject so it supports reference counting. As is common for IOKit classes the init() function must be called for each new allocated object as the class constructor is unable to report errors due to lack of C++ exceptions support in IOKit.

This is a kernel mode only code, it can't be used for user mode projects without modification.

The sparse file is implemented as a storage over a general file. The B-Tree is used to store and access data.

To use DldSparseFile class you should include all files from the src directory to your project. The repository contains an IOKit module project that is provided only for your convinience so you can check that files are compiled as a standalone project in your build environment.

##Usage

Before the first usage the static DldSparseFile::sInitSparseFileSubsystem() initialization routine should be called. The counterpart routine DldSparseFile::sFreeSparseFileSubsystem() should be called on IOKit module unloading or after all DldSparseFile objects have been freed.

To create a sparse file backed by a file on a filesystem call the static function

 ```
    static DldSparseFile*  DldSparseFile::withPath( __in const char* sparseFilePath, // path to a file
                                                    __in_opt vnode_t cawlCoveringVnode, // set to NULL
                                                    __in_opt unsigned char* cawlCoveredVnodeID, // set to NULL
                                                    __in_opt const char* identificationString ); // set to NULL
  ```
 The sparseFilePath parameter is a path to a file on any mounted filesystem.
 You should ignore cawlCoveringVnode, cawlCoveredVnodeID and identificationString and set them to NULL. These parameters are optional and are used in MacOSX-Kernel-Filter project. Refer to MacOSX-Kernel-Filter ( https://github.com/slavaim/MacOSX-Kernel-Filter ) project for more information.
  
 To perform file IO(read/write) call
 
 ```
  void DldSparseFile::performIO( __inout IoBlockDescriptor*  descriptors,
                                 __in unsigned int dscrCount );
 ```
 
 For an example of sparse IO file usage see DldCoveringFsd::rwData function implementation at https://github.com/slavaim/MacOSX-Kernel-Filter/blob/master/DLDriver/DldCoveringVnode.cpp#L1133
