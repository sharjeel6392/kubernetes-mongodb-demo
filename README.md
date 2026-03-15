# Kubernetes Demo: MongoDB + Mongo Express Deployment

This project demonstrates how to deploy a simple <b>two-tier web application</b> on <b>Kubernetes</b> using core Kubernetes resources such as <b>Deployments, Services, ConfigMaps, and Secrets.</b><br>
The application stack consists of:
- <b> MongoDB </b> - a NoSQL database used for data storage
- <b> Mongo Express </b> - a web-based MongoDB admin interface
The goal of this project is to showcase how Kubernetes components interact to run a containerized application and expose it to users while maintaining secure configuration management.

## Architecture Overview
This simple deployment includes the following Kubernetes resources:
| Resource | Count | Purpose |
| ---- | ---- | ---- | 
| Deployments | 2 | Run MongoDB and Mongo Express containers |
| Pods | 2 | Each Deployment manages one Pod |
| Services | 2 | Enable communication between components |
| ConfigMap | 1 | Stores non-sesitive configurations |
| Secret | 1 | Stores sensitive credentials|

The architecture represents a <b>simple client → web app → database pattern</b>, commonly seen in real-world microservice sustems.

## System Components
### MongoDB Deployment
MongoDB serves as the backend database for the application. It runs inside a Kubernetes <b>Pod managed by a Deployment</b>.

The Deployment ensures:
- The databse container is always running
- Kubernetes can restart the container if it fails
- The configuration remains declarative

Since the database should <b>not be exposed externally</b>, it is accessed only within the cluster.

### MongoDB Internal Service
To allow other applications inside the Kubernetes cluster to communicate with MongoDB, we create a <b> ClusterIP Service</b>.

This internal service:
- Provides a <b>stable network endpoint</b>
- Enables communication between Pods
- Prevents external access to the datanase

The Mongo Express application uses this internal service to connect to MongoDB.

### Mongo Express Deployment
Mongo Express is a lightweight web interface for MongoDB that allows users to:
- Browse databases
- Manage collections
- Run queries
- Inspect data

This component runs as a <b>separate Deployment</b> in the cluster.

In order to connect to MongoDB, Mongo Express requires:
- The <b>database connection URL</b>
- <b>Authentication credentials</b>

Instead of hardcoding these values, we use Kubernetes configuration objects (ConfigMap and Secret).

### ConfigMap
The <b>ConfigMap</b> stores non-sensitive configuration values.

In this project, it containes:
- The <b>MongoDB Service URL</b>

Mongo Express reads this value as an <b>environment variable</b> when the container starts.

This approach keeps configuration <b>separate from application code</b>, which is a recommended Kubernetes practice.

### Secret
Sensitive information such as database credentials should not be stored in plain configuration files. Kubernets <b>Secrets</b> provide a secure way to store sensitive data. 

In this project, the Secret stores:
- MongoDB <b>username</b>
- MongoDB <b>password</b>

These credentials are injected into the Mongo Express container through <b>environment variables</b>.

### Mongo Express External Service
To allow users to access Mongo Express from a browser, we create an <b>external Service</b>.

This Service:
- Exposes the Mongo Express Pod outside the cluster
- Maps a <b>node IP and port</b> to the application
- Allows HTTP requests from users

Once deployed, users can open Mongo Express in their browser using:

```bash
http://<NodeIP>:<NodePort>
```

## Request Flow

<!-- add an image -->
### Detailed Flow
1. A user accesses the application through their <b>web browser</b>.
2. The request is routed to the <b>Mongo Express external service</b>.
3. The Service forwards the request to the <b>Mongo Express Pod</b>.
4. Mongo Express connects to MongoDB using:
   - Database URL from the <b>ConfigMap</b>
   - Credentials from the <b>Secret</b>
5. The request is forwarded to the <b>MongoDB Pod</b> through the internal service.
6. MongoDB authenticates the request and returns the response.

## Project Structure

## Deployment Steps

## Verifying the Deployment

## Key Kubernetes Concepts Demonstrated
This project demonstrates several fundamental Kubernetes concepts:
- Container orchestration
- Service discovery
- Configuration management
- Secure secret handling
- Internal vs External Service

This concepts form the foundation of deploying real-world applications on Kubernetes.