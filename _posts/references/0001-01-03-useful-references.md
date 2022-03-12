---
layout: reference
title:  "Interesting Collection of Information"
tags:
- bookmarks
- references
---

# Kubernetes

## Service Mesh

### eBPF

- [Cilium Open Source Project](https://cilium.io)
- [How eBPF will solve Service Mesh - Goodbye Sidecars (Isovalent)](https://isovalent.com/blog/post/2021-12-08-ebpf-servicemesh)
- [How eBPF Streamlines the Service Mesh (Isovalent via The New Stack)](https://thenewstack.io/how-ebpf-streamlines-the-service-mesh/)
- [Super Charge Your Service Mesh with eBPF (Solo.io via ServiceMeshCon NA 2021)](https://youtu.be/VV3MzbPXyTM)

- [Calico eBPF via Tigera](https://www.tigera.io/blog/introducing-the-calico-ebpf-dataplane/) - with lots of contrasting performance data.
- [GKE Dataplane v2](https://cloud.google.com/kubernetes-engine/docs/concepts/dataplane-v2)
- [AWS: Turbocharging EKS networking with Bottlerocket, Calico, and eBPF](https://aws.amazon.com/blogs/containers/turbocharging-eks-networking-with-bottlerocket-calico-and-ebpf/)

## Cloud Native Community

**Networking**
- [Kubernetes Networking Demystifed: a brief guide](https://www.cncf.io/blog/2020/01/30/kubernetes-networking-demystified-a-brief-guide/)
- [Original "Demystified" posting at StackRox](https://www.stackrox.io/blog/kubernetes-networking-demystified/)
- [Tracing the path of network traffic in Kubernetes](https://learnk8s.io/kubernetes-network-packets)

**Bare Metal**
- [Provision Bare Metal Kubernetes with the Cluster API](https://thenewstack.io/provision-bare-metal-kubernetes-with-the-cluster-api/) using [MaaS](https://maas.io)

**Storage**
- [Cloud Native Storage with OpenEBS - Admin Magazine](https://www.admin-magazine.com/Archive/2021/62/Cloud-native-storage-with-OpenEBS)

**etcd**
- [How etcd works and 6 tips to keep in mind](https://blog.px.dev/etcd-6-tips/)
- [A Closer Look at Etcd: The Brain of a Kubernetes Cluster](https://betterprogramming.pub/a-closer-look-at-etcd-the-brain-of-a-kubernetes-cluster-788c8ea759a5)

## Cisco
- [A Comprehensive Guide to Kubernetes Networking with the Intersight Kubernetes Service White paper](https://www.cisco.com/c/en/us/products/collateral/cloud-systems-management/intersight/comp-guide-kubernetes-networking-wp.html) ([PDF 2021-11-08](/artifacts/downloads/comp-guide-kubernetes-networking-wp.pdf))

## Hashicorp

- [Let's Encrypt Hashicorp Nomad and Consul](https://github.com/letsencrypt/hashicorp-lessons)

## Solo

- [Blocking Log4j with Gloo Mesh](https://www.solo.io/blog/blocking-the-log4shell-vulnerability-with-gloo-mesh/)
- [Christian Posta Blogs](https://blog.christianposta.com)
    - [Do I need an API Gateway if I have a service mesh](https://blog.christianposta.com/microservices/do-i-need-an-api-gateway-if-i-have-a-service-mesh/)


# Applications, DevOps, and more

## Development

- [The 12-factor Application](https://12factor.net)
- [OpenAPI Spec](https://github.com/OAI/OpenAPI-Specification)
- Swagger
  - [OpenAPI Guide](https://swagger.io/docs/specification/about/)
  - [Swagger Hub](https://support.smartbear.com/swaggerhub/docs/index.html)
  - [Swagger Editor](https://editor.swagger.io)
  - [Swagger UI](https://swagger.io/tools/swagger-ui/)
  - [Swagger Codegen](https://github.com/swagger-api/swagger-codegen)
- Redocly
  - [OpenAPI repo on GitHub](https://github.com/Redocly/create-openapi-repo)
- [JSON Placeholder](https://jsonplaceholder.typicode.com)
- [Nicholas Jackson's Fake Service](https://github.com/nicholasjackson/fake-service)

## GitHub

- [Sample GitHub Actions](https://github.com/actions/starter-workflows)

## Build Environments

- [Vagrant](https://www.vagrantup.com)
    - [Open Source ESXi Vagrant Provider](https://github.com/nsidc/vagrant-vsphere/)
- [Ruby 2.7.3 GitHub Pages build environment](https://github.com/broadcaststorm/blog-local-build)

# Linux

## Performance/Monitoring

- [Brendan Gregg](https://www.brendangregg.com/)

# Networking

## VXLAN EVPN

### RFCs

- VXLAN [RFC 7348](https://datatracker.ietf.org/doc/html/rfc7348)
- BGP MPLS-based Ethernet VPN [RFC 7432](https://datatracker.ietf.org/doc/html/rfc7432)
- Network Virtualization Overlays [RFC 8365](https://datatracker.ietf.org/doc/html/rfc8365)
- Integrated Routing and Bridging [RFC 9135](https://datatracker.ietf.org/doc/html/rfc9135)
- IP Prefix Advertisement in Ethernet VPN [RFC 9136](https://datatracker.ietf.org/doc/html/rfc9136)
- Internet Exchange BGP Route Server [RFC 7947](https://datatracker.ietf.org/doc/html/rfc7947)
- Multi-site EVPN based VXLAN using Border Gateways [draft-sharma-multi-site-evpn](https://datatracker.ietf.org/doc/html/draft-sharma-multi-site-evpn)
- Multicast in MPLS/BGP IP VPNs [RFC 6513](https://datatracker.ietf.org/doc/html/rfc6513)
- BGP Encodings and Procedures for Multicast in MPLS/BGP IP VPNs [RFC 6514](https://datatracker.ietf.org/doc/html/rfc6514)
- Seamless Multicast Interoperability between EVPN and MVPN PEs [draft-sajassi-bess-evpn-mvpn-seamless-interop-04](https://datatracker.ietf.org/doc/html/draft-sajassi-bess-evpn-mvpn-seamless-interop)
- NVO3 Fault Management [draft-tissa-nvo3-oam-fm](https://datatracker.ietf.org/doc/html/draft-tissa-nvo3-oam-fm)

### NX-OS

- [VXLAN/EVPN Forwarding Characteristics](https://www.ciscopress.com/articles/article.asp?p=2803865)
- ["A Modern, Open and Scalable Fabric: VXLAN EVPN"](https://www.booksprints.net/book/cisco-vxlan-evpn-book-sprint/)

# Automation/Programmability

## RedHat Ansible

- [Ansible Collections Workshop](https://goetzrieger.github.io/ansible-collections/) (AnsibleFest 2020)
- [Training course for Red Hat Automation Platform](https://github.com/ansible/workshops)

## pyATS

- [pyATS Docs](https://developer.cisco.com/docs/pyats/)
- [pyATS API](https://developer.cisco.com/docs/pyats/api/)
- [Jerome Tissieres Intro Article](https://aboutnetworks.net/pyats-1/)

# Blogging/Publishing

## Workshop Style Themes
- https://learn.netlify.com/en/
- https://github.com/matcornic/hugo-theme-learn
- https://github.com/goetzrieger/ansible-labs-summit2020
