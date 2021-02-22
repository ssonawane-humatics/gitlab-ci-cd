# gitlab-ci-cd

image: ros:kinetic-ros-core

stages:
    - build
    - pull
    - test
    - deploy

before_script:
  - apt update >/dev/null && apt install -y git >/dev/null
  - git clone https://gitlab.com/VictorLamoine/ros_gitlab_ci.git >/dev/null
  - source ros_gitlab_ci/gitlab-ci.bash >/dev/null
  - apt install -y liblapack-dev ros-kinetic-navigation >/dev/null

stageOne:
    stage: pull
    script: 
        - mkdir -p ~/catkin_ws/src
        - cd ~/catkin_ws/
        - catkin_make
        - source devel/setup.bash
        - cd src/ 
        - git clone https://github.com/RajPShinde/Autonomous_Wheelchair 
        - cd ~/catkin_ws/
        - catkin_make 
        - catkin_make tests
        - catkin_make run_tests
        - catkin_test_results 
    # artifacts:
    #     paths:
    #         - '/root/catkin_ws/build'
    #         - '/root/catkin_ws/devel'
    #     expire_in: 1 hour
        

stageTwo:
    stage: test    
    script: 
        - echo "This is test"
        # - source /root/catkin_ws/devel/setup.bash
        # - catkin_make tests
        # - catkin_make run_tests
        # - catkin_test_results
    needs:
        - job: stageOne
        #   artifacts: true

stageThree:
    stage: deploy
    script: 
        - echo "This is deploy"
    
