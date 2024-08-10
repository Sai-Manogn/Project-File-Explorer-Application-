#  Project File Explorer Application


# Description
This File Explorer Application is a versatile tool designed to manage files and directories efficiently. It provides comprehensive file manipulation capabilities, including copying, moving, deleting, and creating files. The application also features a powerful search function, permission management, and command processing, all while maintaining a detailed log of activities.


# Table of Contents


•Installation


•Key Features


•Architecture


•Usage

    
  *Code
    
    
  *Sample Run

•Contributing

•License

•References


# Installation

1.**Clone the Repository**:

bash

git clone https://github.com/yourusername/file-explorer.git

2.**Navigate to the Project Directory:**

 cd file-explorer

3.**Build the Project**:

Using a C++ compiler, compile the source files.

   g++ -o file_explorer main.cpp FileSystem.cpp DirectoryManager.cpp FileManager.cpp SearchManager.cpp PermissionManager.cpp CommandProcessor.cpp Logger.cpp

**Run the Application:**

./file_explorer

# Key Features

**File Navigation**

Browse directories and view their contents.

Move up and down the directory tree.

Change the current working directory.

**File Operations**

Create, delete, rename, and move files and directories.

Copy files from one location to another.

View file properties (e.g., size, permissions, modification date).

**Search Functionality**

Search for files or directories by name or other attributes.

Use wildcards or regular expressions for advanced searches.

**File Viewing**

Display the contents of a file directly in the terminal (e.g., using cat, less, or custom implementation).

Open files with appropriate applications (e.g., text files in a text editor).

**Permission Management**

•View and modify file permissions (read, write, execute).

•Change ownership of files (if running as a superuser).

**Command Execution**

•Allow users to execute shell commands directly within the application.

•Integrate with other system tools or utilities.

# Architecture

The application is organized into several key classes, each responsible for specific functionality:

**FileSystem**: Central class managing overall file system operations.

**DirectoryManager**: Handles directory-related tasks like creating, deleting, and navigating directories.

**FileManager**: Manages file-specific operations including copying, moving, deleting, and creating files.

**SearchManager**: Provides the ability to search files by name or content.

**PermissionManager**: Manages and enforces user permissions for file and directory access.

**CommandProcessor**: Interprets and executes user commands.

**Logger**: Records all significant actions within the application to a log file.

# Usage

**Code**

**FileManager.hpp:**


// FileManager.hpp

#ifndef FILE_MANAGER_HPP

#define FILE_MANAGER_HPP

#include <string>

class FileManager {

public:

   void displayMenu();
   
   void handleUserChoice(int choice);

private:
    
 void moveFile();
     
 void copyFile();
    
 void deleteFile();
    
 void createFile();
    
 void searchFile();
    
  void managePermissions();
    
 // Helper functions to get input from the user
    
 std::string getFilePath(const std::string &prompt);
    
 std::string getSearchCriteria();
    
  std::string getDirectory();
    
  std::string getPermissions();
    
  void logOperation(const std::string &operation, const std::string &details);
    
};

#endif // FILE_MANAGER_HPP
    
**FileManager.cpp:**


#include "FileManager.hpp"

#include <iostream>

#include <filesystem>

#include <fstream>

#include <chrono>

#include <iomanip>

#include <sstream> // Include this for std::ostringstream

void FileManager::displayMenu() {

 std::cout << "Available operations:\n";
    
 std::cout << "1. Move file\n";
    
 std::cout << "2. Copy file\n";
    
 std::cout << "3. Delete file\n";
    
 std::cout << "4. Create file\n";
    
 std::cout << "5. Search file\n";
    
 std::cout << "6. Manage file permissions\n";
    
 std::cout << "7. Exit\n";

}

void FileManager::handleUserChoice(int choice) {

 switch (choice) {
    
   case 1: moveFile(); break;
    
   case 2: copyFile(); break;
    
   case 3: deleteFile(); break;
    
   case 4: createFile(); break;
    
   case 5: searchFile(); break;
    
   case 6: managePermissions(); break;
    
   case 7: std::cout << "Exit\n"; break;
    
   default: std::cout << "Invalid choice. Please try again.\n";
    
 }
}

void FileManager::moveFile() {

 std::string source = getFilePath("Enter source file path: ");
    
 std::string destination = getFilePath("Enter destination file path: ");
    
 try {
    
  std::filesystem::rename(source, destination);
        
  std::cout << "File moved successfully.\n";
        
  logOperation("Move", source + " -> " + destination); // Log after success
    
 } catch (const std::filesystem::filesystem_error &e) {
    
   std::cerr << "Error moving file: " << e.what() << '\n';
    
 }

}

void FileManager::copyFile() {

 std::string source = getFilePath("Enter source file path: ");
    
 std::string destination = getFilePath("Enter destination file path: ");
 
 try {
    
  std::filesystem::copy(source, destination);
        
  std::cout << "File copied successfully.\n";
        
   logOperation("Copy", source + " -> " + destination); // Log after success
    
  } catch (const std::filesystem::filesystem_error &e) {
    
  std::cerr << "Error copying file: " << e.what() << '\n';
    
 }

}

void FileManager::deleteFile() {

 std::string filePath = getFilePath("Enter file path to delete: ");
    
try {
    
 std::filesystem::remove(filePath);
        
 std::cout << "File deleted successfully.\n";
        
 logOperation("Delete", filePath); // Log after success
    
 } catch (const std::filesystem::filesystem_error &e) {
    
  std::cerr << "Error deleting file: " << e.what() << '\n';
    
 }

}

void FileManager::createFile() {

 std::string filePath = getFilePath("Enter file path to create: ");
    
 std::ofstream ofs(filePath);
    
 if (ofs) {
    
 ofs.close();
        
 std::cout << "File created successfully.\n";
        
 logOperation("Create", filePath); // Log after success
    
 } else {
    
 std::cerr << "Error creating file: Could not open file.\n";
    
 }

}

void FileManager::searchFile() {

 std::string criteria = getSearchCriteria();
    
 std::string directory = getDirectory();
    
 for (const auto &entry : std::filesystem::recursive_directory_iterator(directory)) {
    
 if (entry.path().filename().string().find(criteria) != std::string::npos) {
        
 std::cout << "Found: " << entry.path() << '\n';
        
  }
    
}
    
 logOperation("Search", criteria + " in " + directory); // Log after search

}

void FileManager::managePermissions() {

 std::string filePath = getFilePath("Enter file or directory path: ");
    
 std::string permissions = getPermissions();
    
 try {
    
 // Convert string permissions to std::filesystem::perms here
        
 std::filesystem::permissions(filePath,
        
                            std::filesystem::perms::owner_all | 
                                     
                            std::filesystem::perms::group_all | 
                                     
                             std::filesystem::perms::others_all);
        
 std::cout << "Permissions modified successfully.\n";
        
 logOperation("ManagePermissions", filePath + " -> " + permissions); // Log after success
    
 } catch (const std::filesystem::filesystem_error &e) {
    
 std::cerr << "Error managing permissions: " << e.what() << '\n';
    
 }

}

std::string FileManager::getFilePath(const std::string &prompt) {

 std::string path;
    
 std::cout << prompt;
    
 std::cin >> path;
    
 return path;

}

std::string FileManager::getSearchCriteria() {

 std::string criteria;
    
 std::cout << "Enter search criteria: ";
    
 std::cin >> criteria;
    
 return criteria;

}

std::string FileManager::getDirectory() {

 std::string directory;
    
 std::cout << "Enter directory to search in: ";
    
 std::cin >> directory;
    
 return directory;

}

std::string FileManager::getPermissions() {

 std::string permissions;
    
 std::cout << "Enter new permissions (e.g., rwxrwxrwx): ";
    
 std::cin >> permissions;
    
 return permissions;

}

void FileManager::logOperation(const std::string &operation, const std::string &details) {

 // Get the current time
    
 auto now = std::chrono::system_clock::now();
    
 std::time_t now_c = std::chrono::system_clock::to_time_t(now);
    
  // Create a string stream to format the log message
    
  std::ostringstream oss;
    
  oss << "[" << std::put_time(std::localtime(&now_c), "%Y-%m-%d %H:%M:%S") << "] " <<
     
 operation << ": " << details << '\n';
    
 std::string logMessage = oss.str();
    
// Print log message to console
    
std::cout << logMessage;
    
// Open the log file
    
std::ofstream logFile("file_manager.log", std::ios::app);
    
 if (logFile) {
    
 // Write the log message to the file
        
  logFile << logMessage;
    
  } else {
    
  std::cerr << "Error: Could not open log file.\n";
    
 }

}


**main.cpp:**


// main.cpp

#include "FileManager.hpp"

#include <iostream>

int main() {

 FileManager fm;
    
 int choice = 0;
    
 while (choice != 7) {
    
 fm.displayMenu();
        
 std::cout << "Enter your choice: ";
        
 std::cin >> choice;
        
 fm.handleUserChoice(choice);
    
 }
    
 return 0;

}


**Sample Run**

Output

![image](https://github.com/user-attachments/assets/4ed2e5df-4b44-45ee-8c45-63195f1ef467)

# Contributing


Contributions are welcome! Please follow these steps:

1. Fork the repository.

2. Create a new branch for your feature or bug fix.

3. Make your changes and ensure the application builds and runs correctly.

4. Submit a pull request for review.

# License


•This '**File exploring Application**' project is licensed under the our group. See the LICENSE file for details.

# References


•Google: For general research and problem-solving.

•YouTube: For tutorials and visual learning.

•Training: Formal and informal training resources.
