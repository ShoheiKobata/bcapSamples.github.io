===================================
Setting PC
===================================

PC で必要な設定の説明

RC8,RC8A,COBOTTAの場合
===============================

推奨環境は以下の通りです。



RC9,COBOTTA Proの場合
===============================
aaaaa



Component Diagram
------------------

.. uml::

  @startuml
  title sample component diagram
  package Server {
    node instance
    node gateway
    instance -- gateway
  }
  package Local {
    node PC {
      node Browser
    }
  }
  Browser -u- gateway
  
  @enduml


Activity Diagram
------------------

.. uml::
  
  @startuml
  title sample activity diagram
  start
  :write document;
  if (any update exists?) then (yes)
  :commit;
  else (no)
  :do nothing;
  endif
  :push;
  end
  @enduml

Gantt Diagram
-------------------

.. uml::
  
  @startuml
  title sample gantt diagram
  Project starts 2022-10-10
  2022/10/14 is colored in salmon
  [setup repo] starts 2022-10-15 and lasts 5 days

  then [write docs] lasts 10 days
  note bottom
  note for write docs
  end note

  then [publish docs] lasts 10 days
  @enduml
