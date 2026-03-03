# Automating-employee-data
Automating Employee Data Management System is designed to streamline and simplify the process of managing employee information within an organization. Traditionally, employee data such as personal details, job roles, department information, attendance, salary records.

function onCondition() {

(function executeRule(current, previous) {

    // Only create user if not already linked
    if (!current.u_user) {

        var userGR = new GlideRecord('sys_user');
        userGR.initialize();

        userGR.first_name = current.u_first_name;
        userGR.last_name  = current.u_last_name;
        userGR.email      = current.u_email;
        userGR.user_name  = current.u_email;
        userGR.department = current.u_department;
        userGR.active     = true;

        var userSysId = userGR.insert();

        // Link Employee to User
        current.u_user = userSysId;
    }

})(current, previous);

(function executeRule(current, previous) {

    if (current.u_user) {

        var roleGR = new GlideRecord('sys_user_has_role');
        roleGR.initialize();
        roleGR.user = current.u_user;

        // Assign ITIL role (change if needed)
        roleGR.role = 'itil'; 
        roleGR.insert();
    }

})(current, previous);

(function executeRule(current, previous) {

    if (current.u_department.changes() && current.u_user) {

        var userGR = new GlideRecord('sys_user');
        if (userGR.get(current.u_user)) {

            userGR.department = current.u_department;
            userGR.update();
        }
    }

})(current, previous);

(function executeRule(current, previous) {

    if (current.u_status == 'Terminated' && previous.u_status != 'Terminated') {

        // Disable user
        if (current.u_user) {

            var userGR = new GlideRecord('sys_user');
            if (userGR.get(current.u_user)) {
                userGR.active = false;
                userGR.update();
            }
        }

        // Close open incidents
        var incGR = new GlideRecord('incident');
        incGR.addQuery('caller_id', current.u_user);
        incGR.addQuery('state', '!=', 7); // Not Closed
        incGR.query();

        while (incGR.next()) {
            incGR.state = 7; // Closed
            incGR.close_notes = 'Auto closed due to employee termination';
            incGR.update();
        }
    }

})(current, previous);

(function runMailScript(current, template, email, email_action, event) {

    template.print("Employee " + current.u_first_name + " " + 
                   current.u_last_name + " has been terminated.");

})(current, template, email, email_action, event);

var empGR = new GlideRecord('u_employee');
empGR.addQuery('u_status', 'On Leave');
empGR.query();

while (empGR.next()) {
    gs.info("Employee on leave: " + empGR.u_first_name);
}

