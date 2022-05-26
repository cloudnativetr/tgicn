# Episode 002: What's new in Kubernetes v1.24?

- Hosted by [@developerguyba](https://github.com/developer-guy)
- 26/05/2022

<!--- Thumbnailed embed of the video, n8Xo_ghCIOSY is the video id from the youtube url --->

<a href="https://www.youtube.com/watch?v=X3msIFiqvvg" target="_blank"><img src="http://img.youtube.com/vi/X3msIFiqvvg/hqdefault.jpg" width="480" height="360" border="10" /></a>

## Table of Contents

## Week in Review

- [Kyverno, Sandbox seviyesinden Incubation seviyesine geçiş için proposal başlattı](https://github.com/cncf/toc/pull/784)
  - [Tweet](https://twitter.com/kyverno/status/1529661078531059713?s=20&t=0BJ3Uy8U-A4SFXOtXX9_rQ)
- [Cloud Native Security White Paper v2 duyuruldu](https://www.cncf.io/blog/2020/11/18/announcing-the-cloud-native-security-white-paper/)
- [Secure Software Factory Reference Architecture Paper duyuruldu](https://www.cncf.io/blog/2022/05/20/announcing-the-secure-software-factory-reference-architecture-paper/)
- [Chainguard takımı ilk ürünlerini duyurdu](https://thenewstack.io/chainguard-enforce-software-supply-chain-security-for-k8s/)
  - [Çoktan video yayını yapıldı bile](https://twitter.com/dnsmichi/status/1524059018507034624)  
  - [everyonecancontribute](https://everyonecancontribute.com/)
- [Çok ilginç bir ürün keşfetttim: fatt](https://twitter.com/developerguyba/status/1529076967252926464?s=20&t=ZIniNXFfaZ1YcZ2SB94i-w)
  - [OCI As Attestations Storage for your packages](https://marcofranssen.nl/oci-as-attestations-storage-for-your-packages) 
- [Tekton Community Summit](https://community.cd.foundation/events/details/cd-foundation-tekton-presents-tekton-community-summit/)
- [Liz Rice rica etti KubeCon NA'nın Call for Proposal (CFP) süresi uzatıldı](https://twitter.com/lizrice/status/1528310359391608834)
  - [Başvurmayı unutma!](https://events.linuxfoundation.org/kubecon-cloudnativecon-north-america/program/cfp/)
- [Keyless Git signing with Sigstore!](https://github.com/sigstore/gitsign)
- [OPA, OCI Imagelarını destekliyor](https://twitter.com/_MarkoB/status/1529414708964511744?s=20&t=ZIniNXFfaZ1YcZ2SB94i-w)
- [Istio build sürecini bazel'den Go'ya geçiriyor](https://twitter.com/kelseyhightower/status/958834738650755072?s=20&t=ZIniNXFfaZ1YcZ2SB94i-w)
- [Tilt, Docker'a katılıyor](https://twitter.com/tilt_dev/status/1529118489310535680?s=20&t=ZIniNXFfaZ1YcZ2SB94i-w)
- [15 Bedava Kubernetes Kursu](https://twitter.com/ghumare64/status/1529687390503153664?s=20&t=iV1uVAwaEkyh7qnVMZ8wiA)
- [KubeCon Valencia 2022 Wrap Up yazısı @richburroughs'dan](https://twitter.com/richburroughs/status/1529571430790139904?s=20&t=iV1uVAwaEkyh7qnVMZ8wiA)

## Show Notes

- [Kubernetes v1.24: Stargazer](https://kubernetes.io/blog/2022/05/03/kubernetes-1-24-release-announcement/)
  - [Kubernetes v1.24 Release Webinar](https://www.youtube.com/watch?v=TNo22-a27oY)
  - [Why is Kubernetes v1.24 release extra special](https://jimangel.io/post/dockershim-kubernetes-v1.24/)
  - [Container Runtime Interface (CRI): Past, Present, and Future](https://www.aquasec.com/cloud-native-academy/container-security/container-runtime-interface/)
  - [KIND v0.13.0 is here](https://twitter.com/BenTheElder/status/1524113640067981312?s=20&t=hAZAXk5GnH8fs8-AQCNVYg)
  - [Kubernetes signals massive adoption of Sigstore for protecting open source ecosystem](https://blog.sigstore.dev/kubernetes-signals-massive-adoption-of-sigstore-for-protecting-open-source-ecosystem-73a6757da73)
  - [Wait, Docker is deprecated in Kubernetes now? What do I do?](https://dev.to/inductor/wait-docker-is-deprecated-in-kubernetes-now-what-do-i-do-e4m)
    - [Detector for Docker Socket (DDS) kubectl plugin](https://github.com/aws-containers/kubectl-detector-for-docker-socket)
- [Deprecated API Migration Guide](https://kubernetes.io/docs/reference/using-api/deprecation-guide/)
  - [Kubernetes Removals and Deprecations In 1.24](https://kubernetes.io/blog/2022/04/07/upcoming-changes-in-kubernetes-1-24/)  
  - [kube-no-trouble: Easily check your clusters for use of deprecated APIs](https://github.com/doitintl/kube-no-trouble)
  - [kubepug](https://github.com/rikatz/kubepug)
- [Verify Signed Container Images](https://kubernetes.io/docs/tasks/administer-cluster/verify-signed-images/)
  - [kubernetes/enhancements#3031](https://github.com/kubernetes/enhancements/issues/3031) 
  - [kubernetes/enhancements#3027](https://github.com/kubernetes/enhancements/issues/3027) 
  - [kubernetes-sigs/bom](https://github.com/kubernetes-sigs/bom)