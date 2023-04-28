<img src="https://avatars.githubusercontent.com/u/40179672" width="75">

[![Tests](https://github.com/openedx-actions/tutor-k8s-configure-autoscaling-policy/actions/workflows/testRelease.yml/badge.svg)](https://github.com/lpm0073/secure-logger/actions)
[![hack.d Lawrence McDaniel](https://img.shields.io/badge/hack.d-Lawrence%20McDaniel-orange.svg)](https://lawrencemcdaniel.com)
[![Open edX Discussion](https://img.shields.io/static/v1?logo=discourse&label=Forums&style=flat-square&color=ff0080&message=discuss.openedx.org)](https://discuss.openedx.org/)
[![docs.tutor.overhang.io](https://img.shields.io/static/v1?logo=readthedocs&label=Documentation&style=flat-square&color=blue&message=docs.tutor.overhang.io)](https://docs.tutor.overhang.io)<br/>
[![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)](https://aws.amazon.com/)
[![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)
[![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white)](https://kubernetes.io/)

# tutor-k8s-configure-autoscaling-policy

Github Action to configure k8s HorizontalPodAutoscaler for a pod.

This action is designed to work seamlessly with the Kubernetes cluster created by the Terraform modules contained in [Cookiecutter Tutor Open edX Production Devops Tools](https://github.com/lpm0073/cookiecutter-openedx-devops).

## Usage:


```yaml
name: Example workflow

on: workflow_dispatch

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # required antecedent
      - uses: actions/checkout@v3.5.0

      # required antecedent
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.THE_NAME_OF_YOUR_AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.THE_NAME_OF_YOUR_AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-2

      # install and configure tutor and kubectl
      - name: Initialize environment
        uses: openedx-actions/tutor-k8s-init@v1.0.8
        with:
          namespace: openedx-prod

      # IMPORTANT: make sure you set the namespace!
      - name: Set the k8s namespace
        shell: bash
        run: kubectl config set-context --current --namespace=openedx-prod

      #
      # ... steps to deploy your Open edX instance to k8s ...
      #

      # This action, overriding default values
      - name: Configure auto scaling for lms pod
        uses: openedx-actions/tutor-k8s-configure-autoscaling-policy@v0.0.1
        with:
          pod-name: lms
          cpu-threshold: 30
          minimum-pods: 2
          maximum-pods: 50

      # This action, overriding default values
      - name: Configure auto scaling for lms worker pod
        uses: openedx-actions/tutor-k8s-configure-autoscaling-policy@v0.0.1
        with:
          pod-name: lms-worker
          cpu-threshold: 30
          minimum-pods: 2
          maximum-pods: 50

      # This action, using all default values
      - name: Configure auto scaling for cms pod
        uses: openedx-actions/tutor-k8s-configure-autoscaling-policy@v0.0.1
        with:
          pod-name: cms

      # This action, using all default values
      - name: Configure auto scaling for cms worker pod
        uses: openedx-actions/tutor-k8s-configure-autoscaling-policy@v0.0.1
        with:
          pod-name: cms-worker
```
