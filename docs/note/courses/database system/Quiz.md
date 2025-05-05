# Quiz 1

$$
\Pi_{name}(\sigma_{building={'白沙一幢'\wedge room='213'\wedge department='CS'}}(student))
$$


$$\Pi_{name}(\sigma_{name\neq'王小强'}(student)\bowtie(\Pi_{building,room}(\sigma_{name='王小强'}(student))))$$
$$\Pi_{A.name,B.name}(\sigma_{A.building=B.building\wedge A.room=B.room \wedge A.sid>B.sid \wedge A.department\neq B.department}(\rho_A(student)\times \rho_B(student)))$$



$$
\Pi_{building,room}(\sigma_{dorm.capacity=R.sum}(dorm\bowtie\rho_{R(building,room,sum)}(building,room~\mathcal{G}_{count(*)}(student))) )
$$