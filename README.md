1️⃣ The Goal
You wanted to:
**Build an application from a Git repository that contains a Dockerfile**
**Deploy it on OpenShift.Make it accessible externally via a route.**

2️⃣ Step-by-Step Flow We Followed
Step 1 — Create a New Build from Git Repo
In the OpenShift UI:
Developer view → +Add → Import from Git.

Enter:

Git Repo URL → your repo containing the Dockerfile.

Select Builder strategy: Docker build (not Source-to-Image, because you already have a Dockerfile).

Specify Dockerfile path (e.g., /Dockerfile if it’s in the root).

Set context directory if Dockerfile isn’t in repo root.

Give your app a Name (e.g., openshift-git).

Choose the namespace/project you want.

Select Target port — in your case 3000/TCP.

Click Create.

🔹 What this does:

Creates a BuildConfig → triggers a Build → produces an image in OpenShift’s internal image registry.

Step 2 — Wait for Build to Complete
Go to Builds → Build Configs → [your app name] → Builds.

Watch the logs until build status is Complete.

The image is stored internally at:

arduino
Copy
Edit
image-registry.openshift-image-registry.svc:5000/<namespace>/<app-name>:latest
Step 3 — Create a Deployment
Initially, your build completed but no deployment existed.
Why? Because OpenShift only creates a deployment automatically if you choose “Deploy the image after build” when creating from Git.

We fixed this by manually creating the Deployment:

+Add → Deploy Image.

Select Image from internal registry.

Choose:

Project: <namespace>

ImageStream: <app-name> (e.g., openshift-git)

Tag: latest

Set port to match your container (3000/TCP).

Click Create.

🔹 What this does:

Creates a Deployment and corresponding ReplicaSet and Pod.

Step 4 — Create Service
If you didn’t create the service during the “Deploy Image” step:

+Add → Service.

Select your deployment as the selector.

Add port mapping:

Service port: 3000

Target port: 3000

Save.

🔹 What this does:

Creates a stable ClusterIP Service to route traffic internally to your Pod.

Step 5 — Create Route (External Access)
+Add → Route.

Select the Service you just created.

Choose:

Target Port: 3000

Hostname: (auto-generated or custom)

Save.

🔹 What this does:

Exposes your service outside the cluster using OpenShift’s router.

Step 6 — Access the Application
Check Route URL in the Topology view or Route section.
Click it → your app should load in the browser.

3️⃣ Final Architecture

[Git Repo w/ Dockerfile]
        │
        ▼
[BuildConfig + Build] → [ImageStream (internal registry)]
        │
        ▼
   [Deployment]
        │
        ▼
   [Pod(s) running container]
        │
        ▼
    [Service] ← internal cluster access
        │
        ▼
    [Route] ← external browser access

4️⃣ Key Points to Remember for Next Time
✅ Always check Build → Deployment → Service → Route in that order.
✅ If build is successful but no deployment, manually deploy the image from internal registry.
✅ Make sure ports match between Dockerfile EXPOSE, Service, and Route.
✅ Use Topology view to visually confirm connections between objects.


