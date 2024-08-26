# Python S3 File Uploader with GUI using `tkinter` and `boto3`

This guide will walk you through creating a Python application that provides a graphical user interface (GUI) using `tkinter` and allows users to upload files to an AWS S3 bucket using the `boto3` library.

### Prerequisites
- Python 3.x installed
- AWS Account
- Basic knowledge of Python
- AWS CLI configured with access to an S3 bucket

### Step 1: Set Up Your Environment

1. **Install Required Libraries**

   First, ensure you have the necessary libraries installed. You can do this via `pip`:

   ```bash
   pip install boto3
   ```

2. **Configure AWS Credentials**

   Ensure that AWS credentials are configured on your local machine. You can do this via the AWS CLI:

   ```bash
   aws configure
   ```

   You will be prompted to enter your AWS Access Key ID, Secret Access Key, default region, and output format.

### Step 2: Create the Python Script

Create a filr named `s3_uploader_gui.py` and copy the Python code below that creates the GUI and uploads the file to S3:

```python
import tkinter as tk
from tkinter import filedialog, messagebox
import boto3
from botocore.exceptions import NoCredentialsError, PartialCredentialsError

class S3UploaderApp:
    def __init__(self, root):
        self.root = root
        self.root.title("S3 Uploader")
        
        # Configure layout
        self.label = tk.Label(root, text="Select a file to upload:")
        self.label.pack(pady=10)
        
        self.upload_button = tk.Button(root, text="Browse", command=self.browse_file)
        self.upload_button.pack(pady=5)
        
        self.upload_button = tk.Button(root, text="Upload to S3", command=self.upload_file)
        self.upload_button.pack(pady=5)
        
        self.file_path = None
        self.bucket_name = "your-s3-bucket-name"  # Replace with your bucket name

    def browse_file(self):
        self.file_path = filedialog.askopenfilename()
        if self.file_path:
            messagebox.showinfo("File Selected", f"Selected file: {self.file_path}")

    def upload_file(self):
        if not self.file_path:
            messagebox.showerror("Error", "No file selected.")
            return

        s3_client = boto3.client('s3')
        try:
            s3_client.upload_file(self.file_path, self.bucket_name, self.file_path.split('/')[-1])
            messagebox.showinfo("Success", f"File {self.file_path} uploaded successfully!")
        except FileNotFoundError:
            messagebox.showerror("Error", "The file was not found.")
        except NoCredentialsError:
            messagebox.showerror("Error", "Credentials not available.")
        except PartialCredentialsError:
            messagebox.showerror("Error", "Incomplete credentials provided.")
        except Exception as e:
            messagebox.showerror("Error", f"An error occurred: {e}")

if __name__ == "__main__":
    root = tk.Tk()
    app = S3UploaderApp(root)
    root.mainloop()
```

### Step 3: Configure Your S3 Bucket

To allow uploads from your application, configure your S3 bucket with appropriate CORS settings and bucket policies.

1. **CORS Configuration**

   Go to the AWS S3 console, select your bucket, and add the following CORS configuration:

   ```json
   [
    {
        "AllowedHeaders": [
            "Authorization"
        ],
        "AllowedMethods": [
            "GET",
            "HEAD"
        ],
        "AllowedOrigins": [
            "*"
        ],
        "ExposeHeaders": [
            "Access-Control-Allow-Origin"
        ]
    }
   ]
   ```

You can also set the CORS using the AWS CLI using the following command:

```bash
aws s3api put-bucket-cors --bucket your-s3-bucket-name --cors-configuration '{"CORSRules" : [{"AllowedHeaders":["Authorization"],"AllowedMethods":["GET","HEAD"],"AllowedOrigins":["*"],"ExposeHeaders":["Access-Control-Allow-Origin"]}]}'
```

   - In the command, replace `"your-s3-bucket-name"` with your actual S3 bucket name.

2. **Bucket Policy**

   Add the following bucket policy to allow uploads (adjust for security as needed):

   ```json
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Principal": "*",
               "Action": [
                   "s3:PutObject",
                   "s3:GetObject"
               ],
               "Resource": "arn:aws:s3:::your-s3-bucket-name/*"
           }
       ]
   }
   ```

   Replace `your-s3-bucket-name` with your actual bucket name.

### Step 4: Running the Application

1. **Replace Bucket Name**: 
   - In the script, replace `"your-s3-bucket-name"` with your actual S3 bucket name.

2. **Run the Script**:
   - Execute the script to launch the GUI application:
   
   ```bash
   python s3_uploader_gui.py
   ```

3. **Interact with the GUI**:
   - Click **Browse** to select a file.
   - Click **Upload to S3** to upload the selected file to your S3 bucket.

`tkinter` is part of the standard library in Python and isn't available as a standalone package via `pip`. If you're using Python on macOS, `tkinter` should come pre-installed with the Python interpreter.

However, if it's missing or not working correctly, the easiest way to ensure you have it is to reinstall Python using a method that includes `tkinter`. Here’s how you can do that:

### Step 1: Install `tkinter` on macOS using Homebrew

1. **Install Homebrew** (if not already installed):
   
   If you don't have Homebrew installed, you can install it by running this command in your terminal:
   
   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

2. **Install Python with `tkinter` via Homebrew**:

   Run the following command to install Python (which includes `tkinter`):
   
   ```bash
   brew install python-tk
   ```

This method ensures that your Python installation includes `tkinter`.

### Step 2: Verify Installation

After installation, you can check whether `tkinter` is installed by running the following in your Python shell:

```python
import tkinter
tkinter._test()
```

If the installation is successful, a small window should appear.

### Best Practices

1. **Security Considerations**:
   - Do not hardcode your AWS credentials in the code. Instead, use the AWS CLI or environment variables.
   - Make sure to configure the bucket policy to only allow necessary permissions. Avoid using wildcards (`*`) in production.

2. **Error Handling**:
   - Implement comprehensive error handling to deal with file selection issues, network errors, and authentication problems.

3. **User Feedback**:
   - Provide clear feedback to users when a file is successfully uploaded or if an error occurs.

4. **Code Organization**:
   - Consider separating the GUI logic from the S3 upload logic for better maintainability and testability.

### References

- [boto3 Documentation](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html)
- [tkinter Documentation](https://docs.python.org/3/library/tkinter.html)
- [AWS S3 CORS Configuration Guide](https://docs.aws.amazon.com/AmazonS3/latest/userguide/cors.html)
- [AWS S3 Bucket Policy Examples](https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-bucket-policies.html)




