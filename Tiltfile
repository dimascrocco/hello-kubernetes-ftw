print('Kawabunga!')

load('ext://color', 'color')
load('ext://helm_resource', 'helm_resource', 'helm_repo')

update_settings(k8s_upsert_timeout_secs = 120) # tempo extra pro redis subir

helm_repo(
    'dapr', 
    'https://dapr.github.io/helm-charts',
    labels=['infra'],
    resource_name='helm-repo-dapr')

helm_resource(
  'dapr',
  'dapr/dapr',
  namespace='dapr-system',
  labels=['system'],
  flags=['--version=1.12.0', '--create-namespace'], #, , '--wait'
  resource_deps=['helm-repo-dapr'])

helm_resource(
  'dapr-dashboard',
  'dapr/dapr-dashboard',
  namespace='dapr-system',
  labels=['system'],
  flags=['--create-namespace'], 
  resource_deps=['helm-repo-dapr'])
k8s_resource('dapr-dashboard', port_forwards="8089:8080", labels=['system'])

# REDIS
# architecture=standalone: não sobe replicas (-30s)
helm_repo(
    'bitnami',
    'https://charts.bitnami.com/bitnami',
    labels=['infra'],
    resource_name='helm-repo-bitnami')
helm_resource(
  'redis',
  'bitnami/redis',
  labels=['system'],
  flags=['--set=architecture=standalone'], # flags=['--set=image.tag=6.2', '--wait'],
  resource_deps=['helm-repo-bitnami'])
# TODO subir redis com "docker run" --> yaml

# REDIS alternative to bitnami
# helm_repo(
#     'pascaliske',
#     'https://charts.pascaliske.dev',
#     labels=['infra'],
#     resource_name='helm-repo-pascaliske')
# helm_resource(
#   'redis',
#   'pascaliske/redis',
#   labels=['system'],
#   resource_deps=['helm-repo-pascaliske'])

k8s_yaml('deploy/redis.yaml')
k8s_yaml('deploy/node.yaml')
k8s_yaml('deploy/python.yaml')

k8s_resource('nodeapp', labels=['app'], resource_deps=['dapr', 'redis'])
k8s_resource('pythonapp', labels=['app'], resource_deps=['nodeapp'])
