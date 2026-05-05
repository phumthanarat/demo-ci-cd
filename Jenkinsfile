apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-n8n
  namespace: n8n
  uid: 6b9332af-600f-41d0-8a60-f0b10e4e7148
  resourceVersion: '37928386'
  generation: 19112
  creationTimestamp: '2025-06-16T09:44:00Z'
  labels:
    app.kubernetes.io/instance: my-n8n
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: n8n
    app.kubernetes.io/version: 1.76.1
    helm.sh/chart: n8n-1.0.0
    k8slens-edit-resource-version: v1
  annotations:
    deployment.kubernetes.io/revision: '40'
    meta.helm.sh/release-name: my-n8n
    meta.helm.sh/release-namespace: n8n
  selfLink: /apis/apps/v1/namespaces/n8n/deployments/my-n8n
status:
  observedGeneration: 19112
  replicas: 1
  updatedReplicas: 1
  readyReplicas: 1
  availableReplicas: 1
  conditions:
    - type: Progressing
      status: 'True'
      lastUpdateTime: '2025-10-29T02:01:38Z'
      lastTransitionTime: '2025-06-16T09:44:00Z'
      reason: NewReplicaSetAvailable
      message: ReplicaSet "my-n8n-7596f8d785" has successfully progressed.
    - type: Available
      status: 'True'
      lastUpdateTime: '2025-10-29T08:01:50Z'
      lastTransitionTime: '2025-10-29T08:01:50Z'
      reason: MinimumReplicasAvailable
      message: Deployment has minimum availability.
spec:
  replicas: 1
  selector:
    matchLabels:
      app.kubernetes.io/instance: my-n8n
      app.kubernetes.io/name: n8n
      app.kubernetes.io/type: master
  template:
    metadata:
      creationTimestamp: null
      labels:
        app.kubernetes.io/instance: my-n8n
        app.kubernetes.io/name: n8n
        app.kubernetes.io/type: master
      annotations:
        checksum/config: 65157d060b037e34aa8b0286b5e0e2800e064129456432e58033eaa79278fb14
        kubectl.kubernetes.io/restartedAt: '2025-10-28T15:16:15+07:00'
    spec:
      volumes:
        - name: data
          persistentVolumeClaim:
            claimName: my-n8n
      containers:
        - name: n8n
          image: n8nio/n8n:1.117.3
          ports:
            - name: http
              containerPort: 5678
              protocol: TCP
          env:
            - name: WEBHOOK_TUNNEL_URL
              value: https://automate.thaisamut.co.th
            - name: N8N_HOST
              value: automate.thaisamut.co.th
            - name: N8N_PROTOCOL
              value: https
            - name: WEBHOOK_URL
              value: https://automate.thaisamut.co.th
            - name: N8N_PORT
              value: '5678'
            - name: WEBHOOK_URL
              value: https://automate.thaisamut.co.th/rest/oauth2-credential/callback
            - name: WEBHOOK_URL
              value: https://automate.thaisamut.co.th/
          resources:
            limits:
              cpu: 600m
              memory: 1Gi
            requests:
              cpu: 600m
              memory: 1Gi
          volumeMounts:
            - name: data
              mountPath: /home/node/.n8n
          livenessProbe:
            httpGet:
              path: /healthz
              port: http
              scheme: HTTP
            timeoutSeconds: 1
            periodSeconds: 10
            successThreshold: 1
            failureThreshold: 3
          readinessProbe:
            httpGet:
              path: /healthz
              port: http
              scheme: HTTP
            timeoutSeconds: 1
            periodSeconds: 10
            successThreshold: 1
            failureThreshold: 3
          lifecycle: {}
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
          imagePullPolicy: IfNotPresent
          securityContext: {}
      restartPolicy: Always
      terminationGracePeriodSeconds: 30
      dnsPolicy: ClusterFirst
      serviceAccountName: my-n8n
      serviceAccount: my-n8n
      securityContext:
        runAsUser: 1000
        runAsGroup: 1000
        runAsNonRoot: true
        fsGroup: 1000
      schedulerName: default-scheduler
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 25%
      maxSurge: 50%
  revisionHistoryLimit: 10
  progressDeadlineSeconds: 600
