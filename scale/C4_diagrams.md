# Context diagram
```puml
@startuml
!includeurl C4_Context.puml

Person(teacher, "Teacher")
Person(student, "Student")

System(korekto, "Korekto", "Grades student exercises")

System_Ext(github, "GitHub", "Git service host")
SystemDb_Ext(db, "Google drive", "Store state of grades and deadlines")

Rel(teacher, korekto, "Create exercise")
Rel(student, korekto, "Register into, look up grades")
Rel(student, github, "Publish exercices")


Rel(korekto, github, "Pull exercices from")
Rel(korekto, github, "Publish feedbacks / issues")
Rel(korekto, db, "Read & Write state", "HTTPS")
@enduml
```

# Container diagram v0

```puml
@startuml
!includeurl C4_Container.puml

title Container diagram V0

Person(teacher, "Teacher")
Person(student, "Student")

System_Boundary(c1, "Korekto") {
    Container(server, "Korekto", "Spring-Boot, Kotlin", "Authenticate users.\nGrade exercises every 4 hours.\nServes website.")
}

System_Ext(github, "GitHub", "Git service host")
SystemDb_Ext(db, "Google drive", "Store state of grades and deadlines")

Rel(teacher, server, "Create exercise", "HTTPS")
Rel(student, server, "Register into, look up grades", "HTTPS")
Rel(student, github, "Publish exercices", "SSH")
Rel(server, db, "Read & Write state", "HTTPS")

Rel(server, github, "Pull exercices from", "SSH")
Rel(server, github, "Publish feedbacks / issues", "HTTPS")

@enduml
```

# Container diagram v1 (load-balancer)

```puml
@startuml
!includeurl C4_Container.puml

!define ContainerC(e_alias, e_label, e_techn, e_descr) collections "==e_label\n//<size:TECHN_FONT_SIZE>[e_techn]</size>//\n\n e_descr" <<container>> as e_alias

title Container diagram V1

Person(teacher, "Teacher")
Person(student, "Student")

System_Boundary(c1, "Korekto") {
    Container(lb, "Load-Balancer", "HAProxy")
    ContainerC(server, "Korekto\ninstances", "Spring-Boot, Kotlin", "Authenticate users.\nServes website.\nGrade exercises every 4 hours.")
}

System_Ext(github, "GitHub", "Git service host")
SystemDb_Ext(db, "Google drive", "Store state of grades and deadlines")

Rel(teacher, lb, "Create exercise", "HTTPS")
Rel(student, lb, "Register into, look up grades", "HTTPS")
Rel(student, github, "Publish exercices", "SSH")

Rel(lb, server, "Dispatch calls", "HTTP")
Rel(server, db, "Read & Write state", "HTTPS")

Rel(server, github, "Pull exercices & Publish issues", "SSH")

@enduml
```

# Container diagram v2 (extract batch part)

```puml
@startuml
!includeurl C4_Container.puml

!define ContainerC(e_alias, e_label, e_techn, e_descr) collections "==e_label\n//<size:TECHN_FONT_SIZE>[e_techn]</size>//\n\n e_descr" <<container>> as e_alias

title Container diagram V2

Person(teacher, "Teacher")
Person(student, "Student")

System_Boundary(c1, "Korekto") {
    Container(lb, "Load-Balancer", "HAProxy")
    ContainerC(server, "Korekto Web\ninstances", "Spring-Boot, Kotlin", "Authenticate users.\nServes website.")
    Container(server2, "Korekto batch", "Spring-Boot, Kotlin", "Grade all exercises every 4 hours")
}

System_Ext(github, "GitHub", "Git service host")
SystemDb_Ext(db, "Google drive", "Store state of grades and deadlines")

Rel(teacher, lb, "Create exercise", "HTTPS")
Rel(student, lb, "Register into, look up grades", "HTTPS")
Rel(student, github, "Publish exercices", "SSH")

Rel(lb, server, "Dispatch calls", "HTTP")
Rel(server, db, "Read & Write state", "HTTPS")

Rel(server2, db, "Read & Write state", "HTTPS")
Rel(server2, github, "Pull exercices & Publish issues", "SSH")

@enduml
```

# Container diagram v3 (remove SPOF)

```puml
@startuml
!includeurl C4_Container.puml

!define ContainerC(e_alias, e_label, e_techn, e_descr) collections "==e_label\n//<size:TECHN_FONT_SIZE>[e_techn]</size>//\n\n e_descr" <<container>> as e_alias

title Container diagram V3

Person(teacher, "Teacher")
Person(student, "Student")

System_Boundary(c1, "Korekto") {
    Container(lb, "Load-Balancer", "HAProxy")
    ContainerC(server, "Korekto Web\ninstances", "Spring-Boot, Kotlin", "Authenticate users.\nServes website & APIs.")
    ContainerC(server2, "Korekto grader", "Spring-Boot, Kotlin", "Grade exercise when notified")
}

System_Ext(github, "GitHub", "Git service host")
SystemDb_Ext(db, "Google drive", "Store state of grades and deadlines")

Rel(teacher, lb, "Create exercise", "HTTPS")
Rel(student, lb, "Register into, look up grades", "HTTPS")
Rel(student, github, "Publish exercices", "SSH")

Rel(lb, server, "Dispatch web calls", "HTTP")
Rel(server, db, "Read & Write state", "HTTPS")
Rel(lb, server2, "Dispatch push events", "HTTP")

Rel(server2, db, "Read & Write state", "HTTPS")
Rel(server2, github, "Pull exercices & Publish issues", "SSH")

Rel(github, lb, "Notify on **push** events")

@enduml
```

# Container diagram v4 (no data relations)

```puml
@startuml
!includeurl C4_Container.puml

!define ContainerC(e_alias, e_label, e_techn, e_descr) collections "==e_label\n//<size:TECHN_FONT_SIZE>[e_techn]</size>//\n\n e_descr" <<container>> as e_alias

title Container diagram V4

Person(teacher, "Teacher")
Person(student, "Student")

System_Boundary(c1, "Korekto") {
    Container(lb, "Load-Balancer", "HAProxy")

    ContainerC(server, "Korekto Web\ninstances", "Spring-Boot, Kotlin", "Authenticate users.\nServes website & APIs.")
    ContainerC(server2, "Korekto grader", "Spring-Boot, Kotlin", "Grade exercise when notified")

    Container(lb2, "Load-Balancer", "HAProxy")
    ContainerC(server3, "Korekto storage API", "Spring-Boot, Kotlin", "serves API to read & write state")
}

System_Ext(github, "GitHub", "Git service host")
SystemDb_Ext(db, "Google drive", "Store state of grades and deadlines")

Rel(teacher, lb, "Create exercise", "HTTPS")
Rel(student, lb, "Register into, look up grades", "HTTPS")
Rel(student, github, "Publish exercices", "SSH")

Rel(lb, server, "Dispatch web calls", "HTTP")
Rel(server, lb2, "Read & Write state", "HTTP")
Rel(lb, server2, "Dispatch push events", "HTTP")

Rel(lb2, server3, "Dispatch calls", "HTTP")
Rel(server3, db, "Read & Write state", "HTTPS")

Rel(server2, lb2, "Read & Write state", "HTTP")
Rel(server2, github, "Pull exercices & Publish issues", "SSH")

Rel(github, lb, "Notify on **push** events")

@enduml
```
