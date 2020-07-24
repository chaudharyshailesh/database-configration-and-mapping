# database-configration-and-mapping by using xml based

1. Hibernate Configuration setup
Make sure we have the hibernate.cfg.xml file configured and added to the project structure in the classpath.

<?xml version='1.0' encoding='utf-8'?>
<!DOCTYPE hibernate-configuration PUBLIC
        "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>
        <!-- Database connection settings -->
        <property name="connection.driver_class">com.mysql.jdbc.Driver</property>
        <property name="connection.url">jdbc:mysql://localhost:3306/sample_db</property>
        <property name="connection.username">root</property>
        <property name="connection.password">root</property>
        <property name="hibernate.dialect">org.hibernate.dialect.MySQLDialect</property>
        <property name="hibernate.show_sql">true</property>
  
        <mapping resource="Student.hbm.xml" />
        <mapping resource="Department.hbm.xml" />
    </session-factory>
</hibernate-configuration>


--------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------------------

2. Hibernate mapping XMLs
Create the hibernate mapping file for each of the entities.

Student.hbm.xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<hibernate-mapping package="com.jcombat.entity">
    <class name="Student" table="student">
        <id name="studentId" column="ID">
            <generator class="native" />
        </id>
        <property name="firstName" column="FNAME" />
        <property name="lastName" column="LNAME" />
         
        <many-to-one name="department" class="com.jcombat.entity.Department" fetch="select">
            <column name="DEPT_ID" not-null="true" />
        </many-to-one>
    </class>
</hibernate-mapping>

-----------------------------------------------------------------------------------------------------------------------------------------
------------------------------------------------------------------------------------------------------------------------------------------------

Department.hbm.xml


<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<hibernate-mapping package="com.jcombat.entity">
    <class name="Department" table="department">
        <id name="depId" column="DEPT_ID">
            <generator class="native" />
        </id>
        <property name="depName" column="DEP_NAME" />
        <set name="students" table="student" inverse="true" cascade="save-update" lazy="true" fetch="select">
            <key>
                <column name="DEPT_ID" not-null="true" />
            </key>
            <one-to-many class="com.jcombat.entity.Student" />
        </set>
    </class>
</hibernate-mapping>


----------------------------------------------------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------------------------------------------------

3. Hibernate Utility class
Create HibernateUtil.java for initial Hibernate configuration which provides us with the SessionFactory isntance.

HibernateUtil.java

package com.jcombat.utility;
  
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;
  
public class HibernateUtil {
    private static final SessionFactory sessionFactory = buildSessionFactory();
      
    @SuppressWarnings("deprecation")
    private static SessionFactory buildSessionFactory() {
        try {
            return new Configuration().configure().buildSessionFactory();
        } catch (Throwable ex) {
            System.err.println("Initial SessionFactory creation failed." + ex);
            throw new ExceptionInInitializerError(ex);
        }
    }
  
    public static SessionFactory getSessionFactory() {
        return sessionFactory;
    }
}

