# Dataset Repository

Welcome to the Dataset Repository! This repository contains a variety of datasets that you can download or access directly.

## Usage

You can either download files or get their contents using the provided Python classes. Below, you'll find instructions on how to use these classes both synchronously and asynchronously.

### Requirements

Make sure you have the following Python packages installed:

- `requests` for synchronous operations
- `httpx` for asynchronous operations

You can install these packages using pip:

```bash
pip install requests httpx
```

## Sync Code
```py
import requests

class SyncFileHandler:
    def __init__(self):
        pass

    def download_file(self, username, repository, file_path, local_filename, branch='main'):
        """
        Download a file from a GitHub repository and save it to a local file.

        Parameters:
        username (str): GitHub username or organization name.
        repository (str): Repository name.
        file_path (str): Path to the file in the repository.
        local_filename (str): The local file path to save the downloaded file.
        branch (str): Branch name to fetch the file from (default is 'main').

        Returns:
        bool: True if the file was downloaded and saved successfully, False otherwise.
        """
        url = f'https://raw.githubusercontent.com/{username}/{repository}/{branch}/{file_path}'

        try:
            response = requests.get(url, stream=True)
            response.raise_for_status()  # Check if the request was successful
            
            with open(local_filename, 'wb') as file:
                for chunk in response.iter_content(chunk_size=8192):
                    if chunk:  # Filter out keep-alive new chunks
                        file.write(chunk)
            
            print(f"File downloaded successfully and saved as {local_filename}.")
            return True
        except requests.exceptions.RequestException as e:
            print(f"Failed to download file: {e}")
            return False

    def get_file_data(self, username, repository, file_path, branch='main'):
        """
        Retrieve the contents of a file from a GitHub repository.

        Parameters:
        username (str): GitHub username or organization name.
        repository (str): Repository name.
        file_path (str): Path to the file in the repository.
        branch (str): Branch name to fetch the file from (default is 'main').

        Returns:
        str: The contents of the file if successful, None otherwise.
        """
        url = f'https://raw.githubusercontent.com/{username}/{repository}/{branch}/{file_path}'

        try:
            response = requests.get(url)
            response.raise_for_status()  # Check if the request was successful
            
            return response.text
        except requests.exceptions.RequestException as e:
            print(f"Failed to retrieve file data: {e}")
            return None

# Example usage:
# sync_handler = SyncFileHandler()
# sync_handler.download_file('username', 'repository', 'path/to/file.txt', 'local_file.txt', branch='main')
# file_data = sync_handler.get_file_data('username', 'repository', 'path/to/file.txt', branch='main')
# print(file_data)
```

## Async Code

```py
import httpx
import asyncio

class AsyncFileHandler:
    def __init__(self):
        pass

    async def download_file(self, username, repository, file_path, local_filename, branch='main'):
        """
        Asynchronously download a file from a GitHub repository and save it to a local file.

        Parameters:
        username (str): GitHub username or organization name.
        repository (str): Repository name.
        file_path (str): Path to the file in the repository.
        local_filename (str): The local file path to save the downloaded file.
        branch (str): Branch name to fetch the file from (default is 'main').

        Returns:
        bool: True if the file was downloaded and saved successfully, False otherwise.
        """
        url = f'https://raw.githubusercontent.com/{username}/{repository}/{branch}/{file_path}'

        async with httpx.AsyncClient() as client:
            try:
                response = await client.get(url)
                response.raise_for_status()  # Check if the request was successful
                
                with open(local_filename, 'wb') as file:
                    file.write(response.content)
                
                print(f"File downloaded successfully and saved as {local_filename}.")
                return True
            except httpx.RequestError as e:
                print(f"Failed to download file: {e}")
                return False

    async def get_file_data(self, username, repository, file_path, branch='main'):
        """
        Asynchronously retrieve the contents of a file from a GitHub repository.

        Parameters:
        username (str): GitHub username or organization name.
        repository (str): Repository name.
        file_path (str): Path to the file in the repository.
        branch (str): Branch name to fetch the file from (default is 'main').

        Returns:
        str: The contents of the file if successful, None otherwise.
        """
        url = f'https://raw.githubusercontent.com/{username}/{repository}/{branch}/{file_path}'

        async with httpx.AsyncClient() as client:
            try:
                response = await client.get(url)
                response.raise_for_status()  # Check if the request was successful
                
                return response.text
            except httpx.RequestError as e:
                print(f"Failed to retrieve file data: {e}")
                return None

# Example usage:
# async def main():
#     async_handler = AsyncFileHandler()
#     await async_handler.download_file('username', 'repository', 'path/to/file.txt', 'local_file.txt', branch='main')
#     file_data = await async_handler.get_file_data('username', 'repository', 'path/to/file.txt', branch='main')
#     print(file_data)

# asyncio.run(main())
```