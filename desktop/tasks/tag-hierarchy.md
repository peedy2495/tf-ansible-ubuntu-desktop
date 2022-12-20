# Used tag-hierarchy in this "desktop" role:

#under construction!
  - ntp # all ntp relevant tasks
    - timesyncd # setup related to timesyncd
  - software-all # everything handles software
    - yq # yaml interpreter
    - k9s # kubernetes tool
    - helm # kubernetes tool
    - terraform # happy gardening ;-)
    - driver-nvidia
    - software-default # basic bouquet
      - software-apt # everything managed by apt
        - upgrade
        - golang  # everyting related to go (incl. pulling Modules)
        - repositories
          - repocleanup # remove everything in sources.list.d
  - pip-requirements # pulling all requirements found in assets/project-reqirements/*.../*.txt
  - go-requirements # pulling all go modules found in assets/project-reqirements/*.../go.mod
  - drives # resizing cloned vm drive root partition
  - etc-hosts # modify hosts file for resolving nexus endpoint
    