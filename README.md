Ansible Role: ansible_role_linux_update
=========

An Ansible role that handles installation of updates on supported distributions.

<ul>
<li>Fedora 34
<li>CentOS 7/8
<li>Red Hat Enterprise Linux 7/8
<li>Debian 10/11
<li>Ubuntu 18/20/22
</ul>

Requirements
------------

For PDF generation wkhtml2pdf is required on host where role is executed.
FIXME

Role Variables
--------------

Available variables are listed below, along with default values where applicable (see defaults/main.yml):

    ansible_role_linux_update_patchwindow: true

(Boolean) Enable patchwindow check. If set to true `ansible_role_linux_update_patchwindow_dow`, `ansible_role_linux_update_patchwindow_nth_day`, and `ansible_role_linux_update_patchwindow_time` would be used to create one specific patchwindow for when the role is allowed to execute. Any execution outside of the window would result in the role halting execution.

    ansible_role_linux_update_patchwindow_dow:

Day of week (short form ex. mon, tue, wed etc.) for patchwindow.

    ansible_role_linux_update_patchwindow_nth_day:

(optional) Nth day of month for patchwindow (integer)

    ansible_role_linux_update_patchwindow_time:

(optional) Timeframe for patchwindow, ex. 18:00-22:00

    ansible_role_linux_update_pre_tasks: []

(optional) List of tasks to be performed before any update tasks. The tasks can consist of three types `command`, `service` and `wait_for` combined in any numbver and order, see example playbook below.

    ansible_role_linux_update_post_tasks: []

(optional) List of tasks to be performed after any update tasks. Uses the same variables as pre_tasks.

    ansible_role_linux_update_categories: []

(optional) List of update_categories, this may differ between distributions, uses the categories available through the distributions package system, ex security etc.

    ansible_role_linux_update_automatic_reboot: false

(boolean) If set to true this would automatically reboot the targeted system if needed after update instalation

    ansible_role_linux_update_automatic_reboot_for_services: false

(boolean) If set to true this would automatically reboot the targeted system if any services need restarting after updates.

    ansible_role_linux_update_webhook_msteams: 

(optional) URL to Microsoft Teams webbhook.

    ansible_role_linux_update_smtp_server:

(optional) IP/FQDN of SMTP server used by the reporting function if the `ansible_role_linux_update_send_email_report` is set to true

    ansible_role_linux_update_smtp_port:

(optional) Port of SMTP server used by the reporting function.

    ansible_role_linux_update_mail_to:

(optional) email address(es) to send email reports to.

    ansible_role_linux_update_mail_from:

(optional) email to set as mail from in the email report funtion.

    ansible_role_linux_update_create_report: false

(boolean) if set to true a update report in PDF format will be created and optionally sent as an attachment in a report email.

    ansible_role_linux_update_omit_report_if_no_updates: false

(boolean) if set to true no reports will be created if there are no available updates.

    ansible_role_linux_update_handle_snapshot: false

(boolean) enable experimental integration of the `ansible_vsphere_snapshot` role to handle VMware snapshots during the updates.

    ansible_role_linux_update_mode:

This role can be run in two modes, check which only check for updates and update which installes updates.

    ansible_role_linux_update_reporting_path: './linux_update_reports/{{ ansible_date_time.date }}'

Path where update reports will be created.

    ansible_role_linux_update_send_email_report: false

(boolean) if set to true an report email will be sent with the update report added as attachment.

Dependencies
------------

This role is dependent on `ansible_role_vsphere_snapshot` if `ansible_role_linux_update_handle_snapshot` is enabled.

Example Playbook
----------------


    - hosts: servers

      vars:
        ansible_role_linux_update_patchwindow: false
        ansible_role_linux_update_automatic_reboot: false
        ansible_role_linux_update_automatic_reboot_for_services: false
        ansible_role_linux_update_create_report: true
        ansible_role_linux_update_omit_report_if_no_updates: false
        ansible_role_linux_update_handle_snapshot: false
        ansible_role_linux_update_mode: update
        ansible_role_linux_update_reporting_path: '/home/someuser/ansible/linux_update_reports/{{ ansible_date_time.date }}'
        ansible_role_linux_update_send_email_report: false

        ansible_role_linux_update_pre_tasks:
        - name: wait for port 22 to be present
            type: wait_for
            port: 22
            state: present
        - name: run a command, this will succeeed
            type: command
            command: touch /somefile1
        - name: wait for a file to be absent
            type: wait_for
            path: /this_will_be_removed_after_some_command
            state: absent
        - name: restart crond
            type: service
            service: cron
            state: restarted

        ansible_role_linux_update_post_tasks:
        - name: run a command, this will succeeed
            type: command
            command: touch /somefile2


      roles:
         - role: ansible_role_linux_update

License
-------

MIT

Author Information
------------------

Mattias Jonsson