# Automating-employee-data
Automating Employee Data Management System is designed to streamline and simplify the process of managing employee information within an organization. Traditionally, employee data such as personal details, job roles, department information, attendance, salary records, 
function onCondition() {

    var state = g_form.getValue('state');

    // 6 = Resolved
    if (state == '6') {

        g_form.setMandatory('resolution_code', true);
        g_form.setMandatory('close_notes', true);

        g_form.showFieldMsg('resolution_code',
            'Resolution Code is mandatory when Incident is Resolved.',
            'error');

        g_form.showFieldMsg('close_notes',
            'Close Notes are mandatory when Incident is Resolved.',
            'error');

        return true;
    } 
    else {

        g_form.setMandatory('resolution_code', false);
        g_form.setMandatory('close_notes', false);

        g_form.hideFieldMsg('resolution_code', true);
        g_form.hideFieldMsg('close_notes', true);

        return false;
    }
}
function onChange(control, oldValue, newValue, isLoading, isTemplate) {

    if (isLoading || newValue == '') {
        return;
    }

    // 6 = Resolved
    if (newValue == '6') {

        g_form.setMandatory('resolution_code', true);
        g_form.setMandatory('close_notes', true);

        if (!g_form.getValue('resolution_code')) {
            g_form.showFieldMsg('resolution_code',
                'Please select Resolution Code before resolving.',
                'error');
        }

        if (!g_form.getValue('close_notes')) {
            g_form.showFieldMsg('close_notes',
                'Please enter Close Notes before resolving.',
                'error');
        }
    }
    else {

        g_form.setMandatory('resolution_code', false);
        g_form.setMandatory('close_notes', false);

        g_form.hideFieldMsg('resolution_code', true);
        g_form.hideFieldMsg('close_notes', true);
    }
}
function onSubmit() {

    var state = g_form.getValue('state');

    if (state == '6') {

        if (!g_form.getValue('resolution_code')) {
            g_form.addErrorMessage('Resolution Code must be filled before resolving.');
            return false;
        }

        if (!g_form.getValue('close_notes')) {
            g_form.addErrorMessage('Close Notes must be filled before resolving.');
            return false;
        }
    }

    return true;
}
(function executeRule(current, previous /*null when async*/) {

    // 6 = Resolved
    if (current.state == 6) {

        if (gs.nil(current.resolution_code)) {
            gs.addErrorMessage('Resolution Code is mandatory when resolving.');
            current.setAbortAction(true);
        }

        if (gs.nil(current.close_notes)) {
            gs.addErrorMessage('Close Notes are mandatory when resolving.');
            current.setAbortAction(true);
        }
    }

})(current, previous);
